# Beispiel einer Übergabe

So sieht das Ergebnis von `/thread` aus. Entscheidend sind die beiden
Abschnitte, die man beim Zusammenfassen sonst weglässt.

```
## Projekt und Ziel
[Projektname] — [was es ist, in zwei Sätzen].

## Aktueller Stand
Live läuft v1.4.2 (Commit a1b2c3d), Branch main, GitHub synchron.
/api/health antwortet 200 mit {"version":"1.4.2"}.

## Die nächste Aufgabe
[Genau EINE Aufgabe, konkret. Nicht „weitermachen".]

## Was bereits bewiesen ist (nicht erneut prüfen)
- Unit-Tests: 412 grün, 3 bekannte Altfehler (X, Y, Z) — daran messen.
- Login und Registrierung in der echten Oberfläche durchgeklickt.

## Was noch NICHT läuft
- Der E2E-Test `checkout.spec.js` ist rot: Zeitüberschreitung in Schritt 4.
- Die Migration `2026_07_add_index` ist geschrieben, aber nie ausgeführt.

## Fallstricke
- `npm run build` muss VOR dem Deploy laufen, sonst ist der Cache-Hash falsch.
- `docker exec` hängt gelegentlich; dann Docker Desktop neu starten.

## Arbeitsweise
    npm test
    bash deploy.sh staging

## Ungeklärt
- Soll die alte API-Version v1 abgeschaltet werden?
```
