# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Familienduell is a browser-based implementation of the German TV game show ("Family Feud"). It is a self-hosted LAN application: one Node.js process serves a static web client and runs a WebSocket relay. All game logic lives in the browser. The same page (`web/index.html`) renders in one of two **roles**, chosen by the user at startup:

- **Display** — the big screen the audience sees (answers hidden behind dots, sounds, intro, animations).
- **Controller** — the host/operator panel that reveals answers, assigns points, triggers sounds, edits questions, etc.

UI text and most identifiers are in German (`frage`=question, `antwort`=answer, `anz`=count/points, `kuerzel`=short label, `runde`=round, `schweinchen`=the "pig"/bonus animation, `Finalmodus`=final round).

## Running

The server lives in `server/` and serves the client from `../web/`.

```bash
cd server
npm install ws        # only dependency; node_modules/ws is already vendored in the repo
node server.js
```

- Web (static client): http://127.0.0.1:8080
- WebSocket relay: port 8081

On Windows, `server/StartServer.bat` runs the bundled `node.exe`. There is also an alternative `server/server.py` (asyncio + `websockets`) contributed for Linux/Python use — note it only opens a WebSocket port and does not serve the static files, so it is not a drop-in replacement for `server.js`.

There is no build step, no linter, and no test suite. Changes to `web/` are picked up by reloading the browser.

## Architecture

### The server is a dumb broadcast relay (`server/server.js`)

The server holds almost no game state. Its entire job:

1. Serve files from `../web/` over HTTP (port 8080).
2. Accept WebSocket connections (port 8081), keep them in a `subscribers[]` array, and **broadcast every received message to all connected clients, including the sender**.
3. Handle one special out-of-band command, `fileOp`, for reading/writing `server/fragen.txt` (the question bank) on the server's disk.

Because broadcast includes the sender, the Controller and Display stay in sync by both reacting to the same message stream. Game logic, scoring, and rendering all happen client-side; the server never interprets gameplay messages.

### Message protocol (`###`-delimited strings)

Every WebSocket message is a plain string split on `###`: `key###value###value2...`. This is the contract between Controller and Display.

- Clients send via `wsSend(key, msg)` in `web/js/websocket.js`.
- Clients receive in the single `ws.onmessage` dispatcher (a long `if/else` on `key`) in `web/js/websocket.js`, which delegates to functions in `web/js/main.js`.
- **To add a new action, you must touch both ends**: add a `wsSend(...)` trigger (usually a click handler wired in `main.js`'s `$(document).ready`) and a matching `key` branch in the `onmessage` dispatcher.

The `fileOp` command is the exception — the server intercepts it instead of blind-broadcasting:
- `fileOp###read###fragen.txt` → server replies with `file###fragen.txt###<content>`.
- `fileOp###write###fragen.txt###<content>` → server writes, then broadcasts `file###...`.

### Questions / `fragen.txt` encoding

`server/fragen.txt` is the persisted question bank. It is **not** plain JSON: it is `btoa(encodeURIComponent(JSON.stringify(...)))` (URI-encoded then base64). Decode with `decodeURIComponent(atob(...))`. See `saveQuestions()` / the `file` branch in `websocket.js`. Each question is `{ frage, kuerzel, antworten: [{ antwort, anz }, ...] }`. Do not hand-edit this file as if it were text.

### Display vs Controller branching

There is no separate Controller page — both roles run the same JS, gated on the global `display` boolean (`true` = Display, set to `false` in the Controller button handler in `websocket.js`). Throughout `main.js` you will see `if (display)` / `if (!display)` guards: the Display animates/plays sound and hides answers behind dots; the Controller shows the real answers and is the only side that originates `setAnswer` / `setAnz` clicks. Sounds also play on the Display by default, or on the Controller machine if "server sound" is enabled (`serverSound`). `?viewonly=true` in the URL auto-joins as a passive Display.

### Key client files

- `web/js/websocket.js` — connection lifecycle, `wsSend`, and the central `onmessage` dispatcher. **Start here** to understand the protocol.
- `web/js/main.js` — all gameplay: question loading/rendering, scoring (`recalcSum`, point multipliers per `runde`), Final mode (`isFinalMode`, two-player scoring), timer, intro, `schweinchen`, the in-browser question editor (`addNewQuestion`/`saveQuestions`), and sound playback.
- `web/js/components.js` — builds the "X" fail markers (three strikes) and wires their click → `setFail`.
- `web/index.html` — single page containing both the start screen and the shared Display/Controller markup, toggled by CSS visibility.
- `web/js/typed.js`, `jquery*.js` — vendored third-party libraries (jQuery, jQuery UI, typed.js for the typing answer animation). Do not edit.

### Game modes

- **Normal rounds**: a question with multiple answers; Controller reveals answers and points; a per-round point multiplier (`runde` / `pointMultiplicator`, set by the "Schweinchen" round buttons) scales the summed points; three fail markers per side.
- **Final mode** (`Finalmodus`, toggled via `#modeFinal`): two players answer five questions; answers and points are shown **only on the Display** (kept hidden from the audience-facing reveal on the Controller). `setPlayer2ForFinalMode` switches which player's column is being filled; `showFinalScores` adds the final-round totals onto the main scoreboard.

## Conventions

- Code style is legacy jQuery + global variables; match the surrounding style rather than modernizing.
- The protocol's correctness depends on Controller and Display interpreting identical messages — when changing rendering, keep both the `display` and `!display` paths consistent, and remember the sender also receives its own broadcast.
