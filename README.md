# Familienduell

![logo](./web/img/logo.png)

Familienduell Client / Server (NodeJs Javascript)

### -A- START DES SERVERS!

___Windows___

1. Im Ordner "server" die Datei StartServer.bat doppelklicken.
   * Im Fenster das jetzt erscheint sollte: "SERVER IS UP AND RUNNING" stehen.
   * Falls die Firewall fragt, solltest du die Anfrage zulassen da sonst der Client nicht verbinden kann.

___Mac___

1. Installiere NodeJs und npm (https://nodejs.org/en/download/)

2. starte die (mac) konsole und wechsle in den Serverordner

3. installiere das Websocketmodul (ws)
`npm install ws`

4. Starte den Server
`node server.js`

5. In der Console erscheint: "SERVER IS UP AND RUNNING"

### -B- START DES CONTROLLERS!

1. Browser öffnen und zur Adresse: http://127.0.0.1:8080 surfen

2. Auf den Controller Button klicken.

### -C- Start des Displays

1. In einem neuen Browserfenster/tab verbinden und auf "Display" klicken.


---------------------------

## ANLEITUNG / SPIELABLAUF

Der **Controller** steuert, der **Display** zeigt. Alles, was du im Controller anklickst, erscheint live auf dem Display. Antworten und Punkte sind im Controller immer sichtbar, auf dem Display bleiben sie hinter Punkten (`....`) verborgen, bis du sie aufdeckst.

### 1. Spielstart (Bereich "Intro")

1. Das Display ist zu Beginn schwarz (Checkbox **Blackscreen** ist aktiv), damit die Zuschauer noch nichts sehen.
2. Klicke **▶ Intro!** – die Titelmusik und das Logo starten.
3. Wenn das Intro durch ist: **■ Intro!** stoppen und die Checkbox **Blackscreen** abwählen, um das Spielfeld freizugeben.

### 2. Frage auswählen (Bereich "Fragenliste")

- Wähle in der **Fragenliste** eine Frage aus (sie wird über ihr Kürzel angezeigt).
- Mit den Pfeilen **▲ / ▼** springst du zur vorherigen/nächsten Frage.
- Beim Frage-Wechsel werden die aufgedeckten Antworten und die Fehler-Markierungen automatisch zurückgesetzt.
- Mit **Frageneditor** legst du Fragen an oder bearbeitest sie; **Speichern!** schreibt sie dauerhaft in die Datei `server/fragen.txt`. Das Drucker-Symbol erzeugt eine Druckansicht aller Fragen.

### 3. Antworten und Punkte aufdecken

- Klicke im Controller auf den **Antworttext**, um die Antwort auf dem Display einzublenden (sie wird Buchstabe für Buchstabe „getippt").
- Klicke auf die **Punktzahl** der Antwort, um deren Punkte aufzudecken. Die Punkte werden in der **SUMME** der aktuellen Frage aufaddiert.
- **Falsche Antwort:** Klicke unten links/rechts auf ein **X** (drei Fehler pro Team möglich). **Clear all Fails!** löscht alle X-Markierungen.

### 4. Punkte an ein Team vergeben

- Die roten Pfeile **‹ / ›** neben der SUMME geben die aktuelle Rundensumme an das **linke** bzw. **rechte** Team. Dabei wird die Summe mit dem aktuellen **Multiplikator** verrechnet und die Rundensumme auf 0 gesetzt.
- **Multiplikator (Bereich "Schweinchen"):** Eine normale Runde zählt einfach (×1). Über **Schweinchen / Schweinchen (2x) / Schweinchen (3x)** spielst du zwischen den Runden die „Schweinchen"-Animation ab und setzt gleichzeitig den Punkt-Multiplikator auf 1, 2 oder 3. Mit **■ Schweinchen** beendest du die Animation und gehst zur nächsten Frage über. Der Multiplikator lässt sich im Feld **Multiplikator** auch direkt eintragen.
- **Manuelle Korrektur (Bereich "Optionen"):** Mit **Punkte Links / Punkte Rechts / Punkte Summe** kannst du jeden Wert direkt setzen (Wert eingeben, **ok** klicken) – praktisch zum Korrigieren.

### 5. Finalrunde (Bereich "Finalmodus")

1. Aktiviere die Checkbox **Finalmodus** (der **Info**-Button erklärt die Besonderheiten). Im Finalmodus werden geklickte Antworten und Punkte **nur auf dem Display** angezeigt.
2. Es gibt 5 Fragen; wähle die aktuelle über das **Frage**-Auswahlfeld (1–5).
3. Schalte mit **Spieler 1 / Spieler 2** um, für welchen Spieler du gerade Antworten/Punkte einträgst.
4. **Timer anzeigen** blendet die Bedenkzeit ein (20 Sek. für Spieler 1, 25 Sek. für Spieler 2), **Timer starten** / **Timer stoppen** steuern den Countdown.
5. Gibt ein Spieler eine Antwort, die nicht in der Liste steht, kannst du sie unter **Alternative Antwort** eintragen (**setzen**) und die zugehörigen **Alt. Punkte** vergeben (**setzen**).

### 6. Spielende (Bereich "Spielende")

- **Show Final Scores** addiert die Punkte aus der Finalrunde auf die Gesamtpunkte der beiden Teams und blendet das Endergebnis ein.


---------------------------

### FAQs:
Q: Warum ist das Display zu Beginn schwarz, und wie kann ich das ändern?   
A: Das Bild ist schwarz damit man die Intromusik starten kann und die Leute (Zuschauer) nicht schon vorher wissen was auf sie zukommt.
   Dies kann (Im Controller) über die Checkbox "Blackscreen", im Bereich "Intro" kontrolliert werden.

Q: Wie funktioniert das mit 2 oder mehr Geräten?   
A: Alle Geräte müssen im gleichen Netzwerk sein. Dann verwende einfach eine Externe IP des Servers (aus dem Serverfenster) zum verbinden von einem anderen Gerät aus.

Q: Läuft der Server mit nodeJs auch unter Linux / Läuft der Python Server auch unter Windows?    
A: Ja, dafür müssen jedoch zunächst die etsprechenden Abhänigkeiten installiert werden.

Q: Gibt es eine Funktion um Leuten über einen Link direkt das Zuschauen zu ermöglichen?   
A: Ja. Der Link lautet: http://SERVERURL/PFAD/index.html?viewonly=true (SERVERURL und PFAD anpassen!)

Q: Ich erhalten im Serverfenster den Fehler: Error: listen EADDRINUSE   
A: Eine andere Anwendung blockiert bereits den Port. Beende diese und starte den Server erneut

Q: Ich habe sonstige Fehler bei (Anzeige, Verbindungsaufbau, Anwendungsverhalten)   
A: Falls du einen anderen Browser als Google Chrome verwendest, installiere Chrome.

Sonstige Fragen, Fehlermeldungen, Beschwerden oder auch Spenden (Paypal) per Mail an: raphael.fritsch@gmx.de

---------------------------

Programmierung: cracker
Unterstützt durch: DiggerTigger, Anni & Naseile

Danke auch an: 
 * [flbe](https://github.com/flbe) für den Python Server
 * [susgo](https://github.com/susgo) für den Finalmodus
 * [ConvalAtGit](https://github.com/ConvalAtGit) für viele Verbesserungen, Redesign und zusätzliche Word und Excel Dokumente
 * [fruitcoder](https://github.com/fruitcoder/) Design verbesserungen