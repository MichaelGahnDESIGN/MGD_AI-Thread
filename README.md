<!-- MGD-HEADER -->
<p align="center"><a href="https://Michael-Gahn.de"><img src="assets/mgd-logo.png" alt="Michael Gahn DESIGN" width="48"></a></p>

<p align="center"><img src="assets/banner.svg" alt="MGD AI Thread" width="100%"></p>

<p align="center">
  <img alt="Lizenz" src="https://img.shields.io/github/license/MichaelGahnDESIGN/MGD_AI-Thread?label=Lizenz">
  <a href="https://github.com/MichaelGahnDESIGN/MGD_AI-Thread/releases/latest"><img alt="Release" src="https://img.shields.io/github/v/release/MichaelGahnDESIGN/MGD_AI-Thread?label=Release"></a>
  <a href="https://Michael-Gahn.de"><img alt="by Michael Gahn DESIGN" src="https://img.shields.io/badge/by-Michael%20Gahn%20DESIGN-cd1616"></a>
</p>
<!-- /MGD-HEADER -->

# MGD — AI-Thread SKILL

Ein Skill für KI-Agenten (**Claude Code** & **ChatGPT Codex**), der eine
Übergabe schreibt, bevor das Kontextfenster voll ist — damit ein neuer Thread
nahtlos weiterarbeitet, statt bei null anzufangen.

Vor der Übergabe sichert er fertige und geprüfte Projektarbeit mit Commit und
Push, wenn ein Remote eingerichtet ist und die Projektregeln das erlauben.
Unfertige Arbeit, fehlgeschlagene Prüfungen und ausstehende Pushes werden in
der Übergabe offen benannt. `/thread` startet keinen produktiven Deploy.

## Das Problem

Lange Arbeitsthreads enden selten sauber. Irgendwann ist der Kontext voll, und
was der Agent über Stunden gelernt hat — welcher Befehl lügt, welche Reihenfolge
stimmen muss, was schon bewiesen ist — geht verloren. Der nächste Thread läuft
in dieselben Sackgassen.

Die naheliegende Lösung, „fass mal zusammen", erzeugt meist eine geschönte
Erfolgsmeldung. Was noch nicht läuft, verschwindet zwischen den Zeilen. Genau
das kostet den nächsten Thread die meiste Zeit.

## Installation

> [!NOTE]
> **👤 FÜR ENTWICKLER** — Der Skill besteht aus einer einzigen Datei
> (`SKILL.md`) plus einer Befehlsdefinition pro Agent. Es gibt keine
> Konfigurationsdatei und keine Abhängigkeiten — Installation heißt: Dateien
> an die richtige Stelle kopieren.

```bash
git clone https://github.com/MichaelGahnDESIGN/MGD_AI-Thread.git
```

**Claude Code** — global:

```bash
mkdir -p ~/.claude/skills/thread
cp MGD_AI-Thread/SKILL.md ~/.claude/skills/thread/SKILL.md
cp MGD_AI-Thread/.claude/commands/thread.md ~/.claude/commands/thread.md
```

> [!NOTE]
> **👤 FÜR ENTWICKLER** — Beide Ziele liegen unter `~/.claude/`, deshalb steht
> `/thread` danach in jedem Projekt zur Verfügung, nicht nur in diesem
> Repository.

**ChatGPT Codex** — `.codex/commands/thread.md` ins Projekt kopieren.

## Erste Schritte

Nach der Installation in einem beliebigen Projekt `/thread` eingeben.

> [!TIP]
> **🤖 FÜR KI-AGENTEN** — Der Befehl sammelt Fakten aus dem Projekt (nicht aus
> der Erinnerung: `git log`, `git status`, `git rev-parse`), bezieht optional
> offene Todos aus dem `/todo`-Skill ein, schreibt die vollständige Übergabe
> in eine Datei und gibt dazu einen kurzen, kopierfertigen Startprompt aus,
> der diese Datei referenziert.

## Die wichtigsten Funktionen/Befehle

| Befehl | Was passiert |
|--------|-------------|
| `/thread` | Erzeugt einen vollständigen Übergabe-Prompt für einen neuen Thread |
| `/thread <thema>` | Grenzt die Übergabe auf ein Thema ein |
| `/thread --kurz` | Nur der Prompt, ohne Belegteil |
| `/thread --datei` | Ohne Wirkung — die Datei entsteht seit v1.1.0 immer |

> [!WARNING]
> **⚠️ FALLSTRICK — die Übergabe zerreißt im Chat.** Bis v1.0 gab der Skill die
> Übergabe als einen großen Codeblock aus. Das funktioniert nicht: Abschnitt 7
> („Arbeitsweise") besteht aus Befehlen, also aus Codeblöcken. Der erste innere
> Zaun beendet den äußeren — die Übergabe zerfällt in Bruchstücke und lässt sich
> nicht mehr am Stück kopieren.
>
> **Seit v1.1.0 landet die Übergabe deshalb immer in einer Datei**
> (`PROJEKT/UEBERGABEN/<JJJJ-MM-TT>-<thema>.md`), und Befehle darin werden
> eingerückt statt gezäunt. Damit überlebt sie auch das Ende des Threads.

### Die fünf Arbeitsschritte und der Git-Zwischenschritt

Laut `SKILL.md` arbeitet der Agent bei jedem Aufruf diese Schritte der Reihe
nach ab:

1. **Fakten sammeln, nicht erinnern** — Stand aus `git log --oneline -12`,
   `git status --short`, `git rev-parse --abbrev-ref HEAD` erheben, ergänzt
   um Versionsdatei, Changelog, Deploy-Marker.
2. **Todos einbeziehen** — ist der `/todo`-Skill vorhanden, offene Punkte per
   `/todo-export` übernehmen und neue Funde vorher per `/todo-add` anlegen.
2a. **Fertige Arbeit sichern** — geprüfte Änderungen getrennt committen und
   auf den vorgesehenen Remote-Branch pushen; Remote-Stand prüfen. Unfertige,
   fremde und sensible Dateien bleiben unangetastet.
3. **Den Prompt schreiben** — in den acht festen Abschnitten unten.
4. **Gegenlesen** — vier Kontrollfragen, bevor der Prompt ausgegeben wird
   (siehe Ehrlichkeitsregeln).
5. **Ausgeben** — immer als Datei unter
   `PROJEKT/UEBERGABEN/<JJJJ-MM-TT>-<thema>.md`, mit eingerückten statt
   gezäunten Befehlen. Danach folgen drei Dinge: der Pfad, ein `cat`-Befehl
   zum Wiedereinlesen — und, seit v1.2.0, ein kurzer **Startprompt**: der
   Satz, den man unverändert als erste Nachricht in den neuen Thread
   einfügt. Er nennt den Dateipfad und die nächste Aufgabe aus Abschnitt 3.
   Wenn das Projekt Übergaben versioniert, wird auch diese geprüfte Datei
   committed und gepusht. Ohne ihn bleibt die Übergabe ein Dokument, das man
   selbst erst noch in eine Anweisung übersetzen muss.

### Die acht Abschnitte des Prompts

| # | Abschnitt | Inhalt |
|---|-----------|--------|
| 1 | Projekt und Ziel | Worum es geht, in zwei bis drei Sätzen |
| 2 | Aktueller Stand | Version, Branch, was live/deployt ist — mit Belegen |
| 3 | Die nächste Aufgabe | Genau EINE, konkret formuliert — nicht „weitermachen" |
| 4 | Was bereits bewiesen ist | Mit Zahlen, damit der neue Thread es nicht erneut prüft |
| 5 | Was noch NICHT läuft | Pflichtabschnitt: offene Fehler, rote Tests, halbfertige Änderungen |
| 6 | Fallstricke | Was in dieser Sitzung Zeit gekostet hat |
| 7 | Arbeitsweise | Befehle zum Bauen, Testen, Ausrollen — wortwörtlich kopierbar |
| 8 | Ungeklärt | Offene Fragen, die der neue Thread stellen sollte |

### Ehrlichkeitsregeln

- Kein „fast fertig". Entweder belegt oder unter „Was noch NICHT läuft".
- Keine Behauptung ohne Beleg: Commit-Hash, Testausgabe, HTTP-Code.
- Eigene Fehlgriffe gehören hinein.

## Grenzen

- Der Skill überwacht das Kontextfenster nicht selbst — der Agent muss
  `/thread` aktiv aufrufen, bevor der Kontext voll ist.
- Die Datei landet im Projekt und ist damit nur so sicher wie das Projekt
  selbst — wer sie über Rechnergrenzen hinweg braucht, muss sie mitnehmen
  (in vielen Projekten ist `PROJEKT/` bewusst nicht eingecheckt).
- Die Faktenbasis in Schritt 1 sind Git-Befehle — für Projekte ohne
  Git-Historie liefert dieser Schritt entsprechend weniger.
- Die Einbindung von Todos setzt den separaten `/todo`-Skill voraus; ohne ihn
  entfällt der Abschnitt schlicht.

## Wiki

| Seite | Inhalt |
|-------|--------|
| [Übersicht](https://github.com/MichaelGahnDESIGN/MGD_AI-Thread/wiki/Home) | Einstieg, Problemstellung, Dateien im Repo |
| [Schnellstart](https://github.com/MichaelGahnDESIGN/MGD_AI-Thread/wiki/Schnellstart) | Installation für Claude Code und ChatGPT Codex, erster Aufruf |
| [Der Übergabe-Prompt](https://github.com/MichaelGahnDESIGN/MGD_AI-Thread/wiki/Der-Uebergabe-Prompt) | Die fünf Arbeitsschritte, die acht Prompt-Abschnitte, die Ehrlichkeitsregeln |
| [Beispiel-Übergabe](https://github.com/MichaelGahnDESIGN/MGD_AI-Thread/wiki/Beispiel-Uebergabe) | Ein vollständiges Beispielergebnis aus dem Repository |

## Verwandte Projekte

| Projekt | Zusammenspiel |
|---------|---------------|
| [MGD_Todo_SKILL](https://github.com/MichaelGahnDESIGN/MGD_Todo_SKILL) | Ist der Skill im selben Projekt vorhanden, zieht `/thread` dessen offene Punkte per `/todo-export` in die Übergabe und legt neue Befunde vorher per `/todo-add` an. Fehlt er, funktioniert `/thread` unverändert — der Schritt entfällt still. |

## Änderungen

Siehe [CHANGELOG.md](CHANGELOG.md). Aktuelle Fassung: **1.3.0**.

## Lizenz

MIT — siehe [LICENSE](LICENSE).

---

## Impressum

Angaben gemäß § 5 DDG — siehe [`IMPRESSUM.md`](IMPRESSUM.md).
