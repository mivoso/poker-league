# tesa Poker League — Spieltag-Tracker

Mobile PWA zum Erfassen eines Pokerabends (Buy-in 10 €, Texas Hold'em) und
automatischer Berechnung von Netto-Bankroll, Tagesrang, Punkten und Saison-Leaderboard.

Statische Single-File-App. Kein Build, keine Abhängigkeiten. Daten liegen im
`localStorage` des jeweiligen Geräts.

## Struktur

```
public/
  index.html              die komplette App
  manifest.webmanifest    PWA-Manifest
  sw.js                   Service Worker (offline-fähig)
  icon-192.png icon-512.png apple-touch-icon.png
firebase.json             Firebase-Hosting-Konfiguration
.github/workflows/deploy.yml   Auto-Deploy auf GitHub Pages bei Push auf main
```

## Variante A — GitHub Pages (empfohlen, kostenlos, kein Account nötig außer GitHub)

```bash
cd poker
git init -b main
git add -A
git commit -m "Poker League Tracker"
gh repo create poker-league --private --source=. --push
```

Danach im Repo: **Settings → Pages → Source: GitHub Actions**.
Der Workflow läuft automatisch. URL danach:
`https://<dein-user>.github.io/poker-league/`

Bei privatem Repo ist Pages je nach GitHub-Plan ggf. nicht verfügbar —
dann `--public` statt `--private` verwenden.

## Variante B — Firebase Hosting

```bash
npm install -g firebase-tools
firebase login
firebase projects:create poker-league-<suffix>   # oder bestehendes Projekt nutzen
firebase use --add
firebase deploy --only hosting
```

`firebase.json` ist bereits konfiguriert (`public/` als Root, kein Caching für
`index.html` und `sw.js`, damit Updates sofort ankommen).

## Auf dem Handy installieren

1. Deploy-URL in Safari (iOS) bzw. Chrome (Android) öffnen
2. Teilen-Menü → **Zum Home-Bildschirm**
3. Startet danach als eigenständige App, offline nutzbar

## Update ausrollen

`public/index.html` ändern → `git push` (Pages) bzw. `firebase deploy` (Firebase).
Die App lädt beim nächsten Start mit Netz automatisch die neue Version.

## Daten

- Speicherung pro Gerät im Browser. Kein Sync zwischen Geräten.
- Verlauf-Tab → **Backup anzeigen** liefert ein JSON aller Events.
- Verlauf-Tab → **Backup wiederherstellen** spielt ein JSON zurück ein.
- Home-Bildschirm-App und normaler Browser-Tab teilen sich denselben Speicher,
  Claude-App und Deploy-URL jedoch nicht.

## Spielregeln, die in der Logik stecken

- Buy-in 10 €, jeder Rebuy weitere 10 €
- Netto = Endstack − (10 € + 10 € × Rebuys)
- Rang nach Netto absteigend
- Punkte nach Tabelle: 1./2./3. immer 15/10/8;
  4. Platz nur ab 8 Spielern (6 Pkt), 5. nur ab 9 Spielern (4 Pkt),
  6. nur ab 10 Spielern (2 Pkt); sonst 0
- Wertung eines Spieltags ab 6 Spielern (Gäste zählen nicht)
- Saison-Qualifikation ab 5 Events
