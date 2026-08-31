---
tags: [skill, obsidian, maintenance, frontmatter]
created: 2026-01-01
status: active
---
# Skill: obsidian-frontmatter-repair

Durchsucht alle Markdown-Dateien in `Brain/` und ergänzt fehlendes oder unvollständiges
Frontmatter nach einem Standardschema. **Löscht oder überschreibt niemals bestehende Felder.**

---

## Scope

`Brain/` rekursiv. Ausnahme: Dateien mit `_`-Präfix werden übersprungen (Hub-Dateien,
deren Frontmatter wird von Hand gepflegt).

---

## Standardschema

```yaml
---
tags: []             # Pflicht — wird aus dem Ordnerpfad abgeleitet, wenn leer
created: YYYY-MM-DD  # Pflicht — aus dem Erstellungsdatum der Datei, wenn fehlend
status: active       # Optional — nur bei Projekt- und Plan-Dateien
---
```

### Tag-Ableitung aus dem Ordnerpfad

| Ordner | Automatischer Tag |
|---|---|
| `Projects/` | `project` |
| `Plans/` | `plan` |
| `Skills/` | `skill` |
| `Skills/<Unterordner>/` | `skill`, `<unterordner>` |
| `Setups/` | `setup` |
| `Decisions/` | `decision` |
| `Knowledge/` | `knowledge` |
| `People/` | `people` |
| `Daily/` | `daily` |
| Root (`Brain/`) | *(kein Tag)* |

---

## Ablauf

### Schritt 0 — Datum messen

Erste Handlung jedes Laufs: Systemzeit abfragen. Dieses Datum gilt für `last-run` und für jedes
`created`, das neu gesetzt wird. **Nicht aus dem Session-Kontext ableiten** — der kann daneben
liegen, und der Fehler ist still.

### Schritt 1 — Vault scannen

Alle `.md` unter `Brain/` rekursiv sammeln, `_`-Dateien herausfiltern.

### Schritt 2 — Pro Datei prüfen

```
□ Hat die Datei überhaupt Frontmatter?
□ Ist "tags" vorhanden und nicht leer?
□ Ist "created" vorhanden?
```

### Schritt 3 — Dry Run ausgeben

```
FRONTMATTER REPAIR — DRY RUN
─────────────────────────────
Dateien geprüft: 23
Dateien ohne Frontmatter: 4
Dateien mit fehlendem "tags": 7
Dateien mit fehlendem "created": 3

Geplante Änderungen:
  + Projects/WebApp.md        → tags: [project], created: 2026-05-10
  + Skills/deploy.md          → tags: [skill]
  + Decisions/api_decisions.md → created: 2026-06-01

Fortfahren? (ja/nein)
```

**Erst nach Bestätigung weitermachen.** Im automatisierten Routine-Lauf ohne Rückfrage fortfahren
und den Dry-Run-Block in den Report übernehmen.

### Schritt 4 — Reparatur durchführen

Pro Datei **separat pro Feld** patchen, Reihenfolge immer `tags` zuerst, dann `created`.

> ⚠️ **Frontmatter-Arrays sind die klassische Falle.** Viele Schreib-Werkzeuge serialisieren ein
> Array als gequoteten String (`tags: '["project"]'` statt `tags: [project]`). Obsidian erkennt
> einen String nicht als Tags — sie erscheinen als `["project"] ×`. Wo ein Werkzeug ein Flag für
> JSON-Serialisierung anbietet, ist auch das **kein verlässlicher Schutz**: nach dem Schreiben
> gegenprüfen, ob wirklich ein YAML-Array dasteht.

**Sicherer Weg:** den kompletten Frontmatter-Block per Lesen–Ersetzen–Schreiben setzen, als
mehrzeilige YAML-Liste:

```yaml
---
tags:
  - plan
created: 2026-07-31
---
```

Dabei den restlichen Dateiinhalt unverändert lassen — die Ersetzung strikt auf den
Frontmatter-Block begrenzen.

#### Sicherheitsprüfung nach jedem Schreibvorgang

```
Datei gegenlesen:
  □ Ist das gesetzte Feld korrekt da — und als Array, nicht als String?
  □ Sind alle vorher vorhandenen Felder noch da?
  □ Ist der Inhalt unterhalb des Frontmatters unverändert?

Bei Abweichung:
  → Datei als "⚠ Patch-Fehler" im Report markieren
  → NICHT weiterpatchen, nächste Datei
```

### Schritt 5 — Report speichern

`Brain/Maintenance/frontmatter-repair-report.md` **komplett überschreiben**:

```markdown
---
last-run: YYYY-MM-DD
status: ok | action-required
tags: [maintenance]
created: YYYY-MM-DD
---

# Frontmatter Repair — YYYY-MM-DD

Dateien geprüft: N
Übersprungen (_-Präfix): N
Bereits OK: N
Repariert: N
Fehler: N

[Alle Dateien haben Frontmatter ✓]

[ODER bei Fehlern:]
  ⚠ Pfad/zur/Datei.md — Patch-Fehler: Feld nach dem Schreiben nicht gefunden
```

**`status`-Regel:** `ok` wenn Fehler = 0, sonst `action-required`.

**Frontmatter des Reports:** `tags: [maintenance]` als echtes YAML-Array; `created` **aus dem alten
Report übernehmen**, nur beim ersten Lauf das heutige Datum. Das komplette Frontmatter gehört in
den Schreib-Payload — nichts nachträglich patchen.

---

## Regeln

- **Niemals** bestehende Frontmatter-Felder überschreiben oder löschen
- **Niemals** Inhalt unterhalb des Frontmatter-Blocks anfassen
- Erst Dry Run, dann Bestätigung (außer im Routine-Lauf)
- Nach jedem Schreibvorgang gegenlesen und verifizieren
- Bei Abweichung: Datei überspringen und als `⚠ Patch-Fehler` melden — **nicht** weiterpatchen
- `tags` immer als echtes YAML-Array schreiben, nie als String

---

## Aufruf

> „Führe obsidian-frontmatter-repair aus"
> „Prüf das Frontmatter im Vault"
> „Welche Dateien haben kein Frontmatter?"
