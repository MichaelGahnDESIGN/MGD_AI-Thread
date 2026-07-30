# MGD — AI-Thread SKILL

Ein Skill für KI-Agenten (**Claude Code** & **ChatGPT Codex**), der eine
Übergabe schreibt, bevor das Kontextfenster voll ist — damit ein neuer Thread
nahtlos weiterarbeitet, statt bei null anzufangen.

---

## Das Problem

Lange Arbeitsthreads enden selten sauber. Irgendwann ist der Kontext voll, und
was der Agent über Stunden gelernt hat — welcher Befehl lügt, welche Reihenfolge
stimmen muss, was schon bewiesen ist — geht verloren. Der nächste Thread läuft
in dieselben Sackgassen.

Die naheliegende Lösung, „fass mal zusammen", erzeugt meist eine geschönte
Erfolgsmeldung. Was noch nicht läuft, verschwindet zwischen den Zeilen. Genau
das kostet den nächsten Thread die meiste Zeit.

## Was dieser Skill macht

| Befehl | Was passiert |
|--------|-------------|
| `/thread` | Erzeugt einen vollständigen Übergabe-Prompt für einen neuen Thread |
| `/thread <thema>` | Grenzt die Übergabe auf ein Thema ein |
| `/thread --kurz` | Nur der Prompt, ohne Belegteil |
| `/thread --datei` | Legt die Übergabe zusätzlich als Markdown-Datei ab |

Der erzeugte Prompt hat acht feste Abschnitte. Zwei davon sind der eigentliche
Kern:

- **Was bereits bewiesen ist** — mit Zahlen, damit der neue Thread es nicht
  erneut prüft.
- **Was noch NICHT läuft** — Pflichtabschnitt. Ist er leer, hat der Agent nicht
  genau genug hingesehen.

## Installation

```bash
git clone https://github.com/MichaelGahnDESIGN/MGD_AI-Thread.git
```

**Claude Code** — global:

```bash
mkdir -p ~/.claude/skills/thread
cp MGD_AI-Thread/SKILL.md ~/.claude/skills/thread/SKILL.md
cp MGD_AI-Thread/.claude/commands/thread.md ~/.claude/commands/thread.md
```

**ChatGPT Codex** — `.codex/commands/thread.md` ins Projekt kopieren.

Danach in einem beliebigen Projekt `/thread` eingeben.

## Zusammenspiel mit `/todo`

Ist der [MGD Todo SKILL](https://github.com/MichaelGahnDESIGN/MGD_Todo_SKILL)
vorhanden, zieht `/thread` die offenen Punkte über `/todo-export` mit in die
Übergabe und legt neue Befunde vorher per `/todo-add` an. So überlebt ein Fund
den Threadwechsel auch dann, wenn der Prompt verloren geht.

Fehlt `/todo`, funktioniert `/thread` unverändert — der Schritt entfällt still.

## Ehrlichkeitsregeln

- Kein „fast fertig". Entweder belegt oder unter „Was noch NICHT läuft".
- Keine Behauptung ohne Beleg: Commit-Hash, Testausgabe, HTTP-Code.
- Eigene Fehlgriffe gehören hinein.

## Lizenz

MIT — siehe [LICENSE](LICENSE).
