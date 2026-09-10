---
name: thread
description: Erzeugt eine vollstaendige Uebergabe, wenn das Kontextfenster knapp wird. Sammelt Projektstand, offene Aufgaben und Fallstricke zu einem Prompt, mit dem ein neuer Thread nahtlos weiterarbeitet. Fuer Claude Code und ChatGPT Codex. Ausloesen mit „/thread".
---

# /thread — Uebergabe an einen neuen Thread

Ein langer Arbeitsthread laeuft irgendwann gegen das Kontextfenster. Dieser Skill
schreibt die Uebergabe, **bevor** das passiert: einen Prompt, der einen frischen
Thread ohne Reibungsverlust weiterarbeiten laesst.

Der haeufigste Fehler bei Uebergaben ist nicht Vergesslichkeit, sondern
Beschoenigung: Was noch nicht laeuft, verschwindet zwischen den Zeilen. Dieser
Skill zwingt deshalb an mehreren Stellen zur Ehrlichkeit.

## Aufruf

```
/thread                 # Uebergabe fuer das aktuelle Projekt
/thread <thema>         # Uebergabe auf ein Thema eingegrenzt
/thread --kurz          # nur der Prompt, ohne Belege
/thread --datei         # ohne Wirkung, die Datei entsteht seit v1.1.0 immer
```

## Was der Skill tun MUSS

Arbeite die Schritte der Reihe nach ab. Ueberspringe keinen.

### Schritt 1 — Fakten sammeln, nicht erinnern

Erinnerung taeuscht, besonders am Ende eines langen Threads. Erhebe den Stand
aus dem Projekt selbst:

```bash
git log --oneline -12
git status --short
git rev-parse --abbrev-ref HEAD
```

Ergaenze, was das Projekt hergibt: Versionsdatei, Changelog, Deploy-Marker,
laufende Dienste. Erfinde nichts. Was du nicht belegen kannst, kommt unter
„Ungeklaert".

### Schritt 2 — Todos einbeziehen

Ist der `/todo`-Skill vorhanden (`.todo-config`, `TODO.html` oder das Kommando
`/todo` selbst), lies die offenen Punkte aus und uebernimm sie in die Uebergabe.

```
/todo-export            # Markdown-Tabelle aller offenen Todos
```

Fehlt der Skill, ueberspringe das schweigend — der Rest funktioniert ohne ihn.
Was in dieser Sitzung neu aufgefallen ist und noch nirgends steht, legst du
vorher an:

```
/todo-add <titel>
```

So ueberlebt ein Befund den Threadwechsel auch dann, wenn die Uebergabe
irgendwann verloren geht.

### Schritt 3 — Den Prompt schreiben

Der Prompt richtet sich an einen Agenten, der **nichts** ueber die Sitzung
weiss. Er braucht diese Abschnitte, in dieser Reihenfolge:

1. **Projekt und Ziel** — worum es geht, in zwei bis drei Saetzen.
2. **Aktueller Stand** — Version, Branch, was live/deployt ist, mit Belegen
   (Commit-Hashes, HTTP-Antworten, Testlaeufe).
3. **Die naechste Aufgabe** — genau EINE, konkret formuliert. Nicht „weitermachen".
4. **Was bereits bewiesen ist** — damit der neue Thread es nicht erneut prueft.
   Mit Zahlen, nicht mit „funktioniert".
5. **Was noch NICHT laeuft** — offene Fehler, rote Tests, halbfertige Aenderungen.
   Dieser Abschnitt ist Pflicht. Ist er leer, hast du nicht genau genug
   hingesehen.
6. **Fallstricke** — was in dieser Sitzung Zeit gekostet hat: Umgebungstuecken,
   Reihenfolgen, die stimmen muessen, Werkzeuge, die luegen.
7. **Arbeitsweise** — Befehle zum Bauen, Testen, Ausrollen. Wortwoertlich
   kopierbar.
8. **Ungeklaert** — offene Fragen, die der neue Thread stellen sollte.

### Schritt 4 — Gegenlesen

Pruefe den Prompt gegen diese Fragen, bevor du ihn ausgibst:

- Koennte jemand ohne diese Sitzung damit arbeiten? Jeder Eigenname, jeder Pfad,
  jeder Befehl muss darin stehen.
- Steht in „Was noch NICHT laeuft" wirklich alles? Auch das Unfertige, das du
  selbst verursacht hast?
- Ist die naechste Aufgabe eine einzelne, oder eine Wunschliste?
- Sind Behauptungen belegt? „Tests gruen" ohne Zahl ist keine Aussage.

### Schritt 5 — Ausgeben

**Schreib die Uebergabe IMMER in eine Datei**, nicht nur in die Antwort:

```
PROJEKT/UEBERGABEN/<JJJJ-MM-TT>-<thema>.md
```

Gibt es den Ordner nicht, leg ihn an; fehlt `PROJEKT/`, nimm das
Projektwurzelverzeichnis. Nenne dem Nutzer den Pfad und gib ihm den Befehl zum
Wiedereinlesen mit:

```bash
cat "PROJEKT/UEBERGABEN/<JJJJ-MM-TT>-<thema>.md"
```

> [!WARNING]
> **⚠️ FALLSTRICK — die Uebergabe zerreisst im Chat.** Frueher lautete dieser
> Schritt „gib den Prompt in einem Codeblock aus". Das funktioniert nicht:
> Abschnitt 7 der Uebergabe besteht aus Befehlen, also aus Codebloecken. Der
> erste innere Zaun (```) beendet den aeusseren — die Uebergabe zerfaellt in
> Bruchstuecke und laesst sich nicht mehr am Stueck kopieren. Genau deshalb ist
> die Datei jetzt der Regelfall und nicht mehr die Option `--datei`.
>
> Innerhalb der Datei: **Befehle um vier Leerzeichen einruecken statt sie zu
> zaeunen.** Eingerueckte Bloecke rendern gleich, koennen aber nichts abbrechen.
> Muss der Text doch im Chat erscheinen (z. B. `--kurz`), umschliesse ihn mit
> **vier** Backticks — dann ueberleben dreifache Zaeune im Inneren.

Nenne dem Nutzer zum Schluss in einem Satz, was der neue Thread als Erstes tun
wird.

## Ehrlichkeitsregeln

- Kein „fast fertig". Entweder es laeuft und ist belegt, oder es steht unter
  „Was noch NICHT laeuft".
- Keine Behauptung ohne Beleg. Commit-Hash, Testausgabe, HTTP-Code.
- Eigene Fehler gehoeren hinein. Ein verschwiegener Fehlgriff kostet den
  naechsten Thread Stunden.
- Lieber ein Satz zu viel zu den Fallstricken als eine Wiederholung derselben
  Sackgasse.


---

## Zusammenspiel mit anderen Skills

Keiner davon ist Voraussetzung — dieser Skill funktioniert allein. Wo einer
vorhanden ist, nutz ihn statt einer Eigenbaulösung.

> [!TIP]
> **🤖 FÜR KI-AGENTEN** — Wenn diese Skills im Projekt verfügbar sind, ruf sie
> an den genannten Stellen auf, statt die Aufgabe selbst nachzubauen.

| Skill | Wofür hier | Quelle |
|---|---|---|
| `/todo` | Befunde festhalten, die über diesen Lauf hinaus wichtig bleiben | [MGD_Todo_SKILL](https://github.com/MichaelGahnDESIGN/MGD_Todo_SKILL) |
| `/graphify` | Für den Abschnitt „Aktueller Stand" liefert der Graph belegte Zahlen statt Erinnerung. | [graphify](https://github.com/Graphify-Labs/graphify) |
| `/autopilot` | Wenn der neue Thread die Arbeit unbeaufsichtigt fortsetzen soll. | [MGD_Autopilot_SKILL](https://github.com/MichaelGahnDESIGN/MGD_Autopilot_SKILL) |

### Empfehlung bei längeren Läufen

> [!TIP]
> **🤖 FÜR KI-AGENTEN** — Zeichnet sich ab, dass die Aufgabe über mehrere
> Schritte geht und der Nutzer nicht daneben sitzt, weis **einmal** auf
> `/autopilot` hin:
>
> > Das hier läuft über mehrere Schritte. Mit `/autopilot` bekommt der Ablauf
> > eine Abbruchbedingung, eine Grundlinie und eine Prüfung nach jeder Änderung —
> > soll ich so weitermachen?
>
> Einmal fragen, Antwort akzeptieren, nicht wiederholen. Bei kurzen, klar
> umrissenen Aufgaben ganz weglassen.
