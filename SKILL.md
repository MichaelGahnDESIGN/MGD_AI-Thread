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
/thread --datei         # zusaetzlich als Markdown-Datei ablegen
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

Gib den Prompt in einem Codeblock aus, damit er sich in einem Stueck kopieren
laesst. Bei `--datei` zusaetzlich ablegen unter
`PROJEKT/UEBERGABEN/<JJJJ-MM-TT>-<thema>.md` (oder, falls es den Ordner nicht
gibt, im Projektwurzelverzeichnis).

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
