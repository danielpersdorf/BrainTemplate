---
tags: [setup, obsidian, onboarding, rollout]
created: 2026-09-04
---
# Anleitung: Second Brain mit Obsidian — einfache Variante

> Kurzfassung für alle ohne Git-Erfahrung, deren Rechner ohnehin den ganzen Tag läuft. Kein Git-Befehl wird selbst benutzt, keine VM, kein SSH-Tunnel — nur Obsidian + MCP auf einem Rechner (Git muss aber installiert sein, s. Voraussetzung — Claude Code braucht es zum Start).

> **Auch ganz ohne Obsidian möglich, mit einer Einschränkung:** Dann brauchst du zwingend **Claude Code** (nicht Claude Desktop — das kommt ohne MCP nicht an lokale Ordner ran). Schritt 1 unten reicht dann schon: Ordner laden, jeder Claude-Code-Session Zugriff darauf geben — Claude liest die `CLAUDE.md` automatisch und kann das Brain direkt lesen/schreiben. Schritte 2–6 (Obsidian, Plugin, MCP) entfallen komplett.

**Voraussetzung:** Claude Desktop bzw. Claude Code ist schon installiert und du bist eingeloggt.
**Zusätzlich muss Git installiert sein** — falls noch nicht vorhanden: https://git-scm.com/downloads → Installer herunterladen, Setup-Wizard durchklicken, überall die Standardeinstellungen belassen. Claude Code braucht Git rein technisch, um überhaupt eine lokale Session zu starten. Du wirst Git selbst nicht benutzen müssen.

## 1. Brain-Vorlage laden

Repo: https://github.com/danielpersdorf/BrainTemplate — das ist das Repo, in dem diese Anleitung liegt.

Entweder selbst clonen, oder auf GitHub → grüner Button **Code** → **Download ZIP** → entpacken und in einen eigenen Ordner speichern, z.B. `C:\Obsidian\MeinBrain`.

## 2. Obsidian installieren

https://obsidian.md herunterladen und installieren.

## 3. Vault öffnen

Obsidian öffnen → **Open folder as vault** → den entpackten Ordner auswählen.

## 4. Plugin installieren

Obsidian → **Settings** → **Community Plugins** → Safe Mode aus → Browse → **Local REST API with MCP** (von Adam Coddington) → installieren + aktivieren.

In den Plugin-Einstellungen (Community Plugins → **Local REST API with MCP** → Zahnrad-Symbol bzw. Options):
- **Binding Host:** `127.0.0.1`
- **Port:** `27124` (Standard belassen)
- Ganz oben steht ein Feld **„API Key"** mit einem automatisch generierten, langen Zeichen-Code (kein eigenes Passwort, wird vom Plugin selbst erzeugt). Daneben ist ein Kopieren-Symbol — anklicken, damit der Key in der Zwischenablage landet. Am besten direkt in Schritt 5 einfügen, solange er noch kopiert ist — die Einstellungsseite muss man sonst später nochmal öffnen, um ihn erneut zu sehen.

## 5. Claude mit dem Vault verbinden

Den Prompt unten in eine Text-/Notizapp kopieren, den Platzhalter durch den in Schritt 4 kopierten API Key **ersetzen** (also den echten Key an dessen Stelle einfügen — der Platzhaltertext darf nicht mehr im Prompt stehen) und erst danach den kompletten Text mit dem eingesetzten Key an Claude Code schicken:

```
Richte die MCP-Verbindung zu meinem Obsidian Vault ein. Ich nutze das Plugin
"Local REST API with MCP", Port 27124, HTTPS, selbst-signiertes Zertifikat.
Mein API Key: <HIER DEN KOPIERTEN KEY EINFÜGEN>

Trage in %USERPROFILE%\.claude.json (Claude Code) und, falls vorhanden, in
%APPDATA%\Claude\claude_desktop_config.json (Claude Desktop) einen
mcpServers-Eintrag "obsidian" ein, der über npx mcp-remote auf
https://127.0.0.1:27124/mcp/ verbindet, mit NODE_TLS_REJECT_UNAUTHORIZED=0
(nötig wegen des selbst-signierten Zertifikats, der Datenverkehr bleibt
lokal). Bestehende Einträge in den Dateien nicht anfassen. Sag mir danach,
wie ich die Verbindung prüfe.
```

Claude braucht dafür Node.js — falls noch nicht vorhanden, einfach bitten, es zu installieren (`node --version` prüft, ob es schon da ist).

> Der API Key gehört in die MCP-Konfiguration, **nicht** in eine Datei im Vault — sonst landet er beim ersten Commit in der Git-Historie.

**Verbindung prüfen:** In einer Claude-Code-Session `/mcp` eingeben → `obsidian · connected`. Bei Claude Desktop: App neu starten, Vertrauensabfrage für „obsidian" bestätigen.

## 6. System-Prompt setzen

Claude Desktop → Settings → Custom Instructions (für Claude Code steckt die Navigation schon in der `CLAUDE.md` im Vault-Root, keine zusätzliche Einstellung nötig):

```
Lies am Anfang jeder Session zuerst mein Obsidian Brain via MCP:
1. CLAUDE.md (Vault-Root)
2. Brain/_INDEX.md
Antworte auf Deutsch.
```

## Fertig

Ab jetzt kann Claude via MCP im Vault direkt lesen und schreiben. Wichtige Erkenntnisse einfach sagen: *„Merk dir das im Brain: …"*

## Wie du das Brain im Alltag nutzt

Du musst nichts eigens anlegen oder pflegen — das passiert von selbst, während du ganz normal mit Claude arbeitest. Claude kann sich dabei zum Beispiel merken:

- **Tagesnotizen (Dailies):** was an einem Tag besprochen und entschieden wurde
- **Entscheidungen:** warum eine Lösung so und nicht anders gewählt wurde, samt Begründung
- **Wissen über Tools/Systeme:** Dinge, die man sonst jedes Mal neu erklären müsste
- **Projektpläne:** du arbeitest mit Claude an einem Plan, er legt ihn im Brain ab und pflegt ihn dort weiter
- **Kontakte:** Kontext und Präferenzen zu Personen, mit denen du regelmäßig zu tun hast

Einfach normal arbeiten und bei Bedarf sagen *„Merk dir das im Brain"* — den Rest übernimmt Claude von selbst. Der Effekt: **jede neue Claude-Session macht direkt da weiter, wo die letzte aufgehört hat** — du musst nichts nochmal erklären, Claude kennt den Stand schon beim Start.

Die einzige Datei, die von Anfang an Inhalt braucht, ist `Brain/Me.md` — Rolle, Stack, Arbeitsweise. Alles andere entsteht beim Arbeiten.

## Kein Git-Wissen nötig — aber installiert muss es sein

Git wird hier nur gebraucht, damit Claude Code überhaupt startet (s. Voraussetzung oben) — du tippst selbst nie einen git-Befehl.

## Optional: eigenes Git-Repo daraus machen (Versionierung + Online-Backup)

Für den Start oben nicht nötig. Wer den Vault zusätzlich versionieren und automatisch online sichern will:

Falls du in Schritt 1 mit **Download ZIP** gearbeitet hast, hat der Ordner noch kein eigenes Git — `git init` reicht direkt. Falls du **geklont** hast, zeigt der Ordner noch auf das BrainTemplate-Repo — vorher die vorhandene `.git` entfernen:

```powershell
cd C:\Obsidian\MeinBrain
Remove-Item -Recurse -Force .git   # nur nötig, falls du in Schritt 1 geklont hast
git init
git add -A
git commit -m "init: brain von BrainTemplate"
```

Eigenes (privates) GitHub-Repo anlegen, dann als Remote eintragen und pushen:

```powershell
git remote add origin https://github.com/DEIN_GITHUB_USER/DEIN_BRAIN_REPO.git
git branch -M main
git push -u origin main
```

> Der GitHub-Vault sollte **privat** sein — er enthält persönliche Arbeitsnotizen und Projektkontext.

Für automatisches tägliches Committen+Pushen (Claude erledigt das, inkl. sprechender Commit-Message) liegt der fertige Ablauf schon im Vault: `Brain/Skills/Obsidian/obsidian-daily-commit-push.md`. Claude bitten, ihn als geplante Aufgabe einzurichten — die übrigen Wartungs-Skills daneben stehen in der README des Repos.

## Häufige Probleme

| Problem | Ursache | Lösung |
|---|---|---|
| `/mcp` zeigt `disconnected` | Obsidian nicht offen oder Plugin nicht aktiv | Obsidian starten, Plugin-Status prüfen |
| `401 Unauthorized` in MCP | Falscher API Key eingetragen | Key in den Plugin-Einstellungen erneut kopieren |
| `NODE_TLS_REJECT_UNAUTHORIZED` Warnung | Bekanntes Verhalten bei selbst-signiertem Zertifikat | Normal — Verbindung bleibt lokal, kein Sicherheitsproblem |
