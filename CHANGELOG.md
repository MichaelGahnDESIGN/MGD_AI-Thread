# Changelog

## 1.2.0 — 2026-09-17

### Hinzugefügt

- **Ein kurzer Startprompt gehört jetzt fest zur Ausgabe.** Bisher endete
  Schritt 5 mit „nenne dem Nutzer in einem Satz, was der neue Thread als
  Erstes tun wird" — das blieb vage genug, dass die Übergabe manchmal nur
  als Datei im Projekt lag, ohne dass irgendwo die eine Nachricht stand,
  die man tatsächlich in den neuen Thread einfügt. Schritt 5 verlangt jetzt
  ausdrücklich drei Dinge: den Dateipfad, den `cat`-Befehl zum
  Wiedereinlesen — und den Startprompt selbst, mit Dateipfad und der
  nächsten Aufgabe aus Abschnitt 3, fertig zum Einfügen als erste Nachricht.

### Warum

Ein Nutzer stellte nach einer erzeugten Übergabe fest, dass er selbst noch
hätte formulieren müssen, wie er damit einen neuen Thread startet — die
Datei allein ist eine Ablage, keine Übergabe. Der kurze Startprompt ist von
dem Codeblock-Fallstrick aus 1.1.0 nicht betroffen: er enthält selbst keine
verschachtelten Befehle und darf normal gezäunt im Chat stehen.

## 1.1.0 — 2026-09-11

### Behoben

- **Die Übergabe zerriss im Chat.** Bis 1.0 gab der Skill das Ergebnis als
  einen großen Codeblock aus. Das kann nicht funktionieren: Abschnitt 7
  („Arbeitsweise") besteht aus Befehlen, also aus Codeblöcken. Der erste innere
  Zaun beendet den äußeren, die Übergabe zerfällt in Bruchstücke und lässt sich
  nicht mehr am Stück kopieren — genau in dem Moment, in dem sie gebraucht wird.

### Geändert

- **Die Datei ist jetzt der Regelfall.** Jede Übergabe wird unter
  `PROJEKT/UEBERGABEN/<JJJJ-MM-TT>-<thema>.md` abgelegt, nicht mehr nur auf
  Wunsch. Der Skill nennt den Pfad und den `cat`-Befehl zum Wiedereinlesen.
- **Befehle in der Übergabe werden eingerückt statt gezäunt** (vier
  Leerzeichen). Eingerückte Blöcke rendern gleich, können aber nichts abbrechen.
- **Muss der Text doch in den Chat**, umschließt ihn der Skill mit vier
  Backticks — dann überleben dreifache Zäune im Inneren.
- `--datei` bleibt als Schalter erhalten, hat aber keine Wirkung mehr, weil das
  Verhalten jetzt Standard ist. Bestehende Aufrufe brechen dadurch nicht.

### Warum

Aufgefallen in einer realen Übergabe: Die fertige Datei enthielt einen
Python-Log-Empfänger, Dart-Code und mehrere Shell-Blöcke. Im Chat kam davon ein
zerrissenes Bruchstück an. Der Fehler steckte nicht in der Übergabe, sondern in
der Anweisung des Skills.

## 1.0.0

Erste öffentliche Fassung: fünf Arbeitsschritte, acht Prompt-Abschnitte,
Ehrlichkeitsregeln, Zusammenspiel mit `/todo`, `/graphify` und `/autopilot`.
