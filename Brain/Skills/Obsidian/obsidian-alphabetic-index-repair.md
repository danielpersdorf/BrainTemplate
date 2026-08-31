---
tags: [skill, obsidian, maintenance, hub]
created: 2026-01-01
status: active
---
# Skill: obsidian-alphabetic-index-repair

Prüft alle Hub-Dateien (`_`-Präfix), ob deren Listen-Einträge alphabetisch sortiert sind, und
repariert die Reihenfolge. **Ändert nur die Reihenfolge von Zeilen — niemals deren Inhalt.**

Hintergrund: Listen-Einträge in Hub-Dateien sind immer alphabetisch sortiert; der Hinweis steht als
Blockquote in jeder Hub-Datei. Dieser Skill stellt die Konvention maschinell sicher.

---

## Scope

Alle `_*.md` unter `Brain/` (rekursiv).

**Geprüft werden nur sortierbare Listen:** Markdown-Tabellen und Bullet-Listen, deren Einträge
Dateien referenzieren (Wikilink am Zeilenanfang bzw. in der ersten Spalte).

**Ausnahmen:**

| Datei / Abschnitt | Regel |
|---|---|
| `Daily/_Daily.md` und die Monats-Hubs `Daily/YYYY-MM/_YYYY-MM.md` | Absteigend chronologisch (neuester oben) — **diese** Reihenfolge prüfen |
| `_INDEX.md`, Abschnitt „Wiederkehrende Patterns & Entscheidungen" | Thematische Prosa-Liste, keine Datei-Referenzliste — überspringen |
| `_PLANS.md` | Tabellen sind nach Status gruppiert — innerhalb jeder Tabelle alphabetisch prüfen |
| Hubs mit fester Prozess-Reihenfolge (Stufe 1 → 2 → 3) | komplett überspringen — die Reihenfolge ist Inhalt |

---

## Sortier-Regeln

- **Case-insensitive** vergleichen: `webApp` und `WebService` gehören zusammen einsortiert
- Sortierschlüssel = sichtbarer Link-/Dateiname des ersten Wikilinks der Zeile (bei `[[Pfad/Name]]` zählt der volle Linktext)
- Ziffern vor Buchstaben (Standard-Ordinalvergleich), Umlaute wie Grundbuchstaben (ä→a, ö→o, ü→u)
- Mehrere Listen in einer Datei werden **jeweils für sich** geprüft — nie Einträge zwischen Abschnitten verschieben

---

## Ablauf

### Schritt 0 — Datum messen

Systemzeit abfragen, bevor irgendetwas geschrieben wird.

### Schritt 1 — Hub-Dateien sammeln

`Glob "Brain/**/_*.md"`, Ausnahme-Dateien aussortieren.

### Schritt 2 — Pro Datei prüfen

```
→ Listen/Tabellen im Body identifizieren
→ Pro Liste: Ist-Reihenfolge mit Soll-Reihenfolge vergleichen
□ Ist der Konventions-Hinweis („🔤 Konvention: …") vorhanden?
  (bei chronologischen Hubs die 📅-Variante)
```

### Schritt 3 — Dry Run ausgeben

```
ALPHABETIC INDEX REPAIR — DRY RUN
──────────────────────────────────
Hub-Dateien geprüft: 19
Bereits korrekt sortiert: 16
Unsortierte Listen: 3
Fehlender Konventions-Hinweis: 1

Geplante Änderungen:
  ~ Setups/_SETUPS.md     → Tabelle „Einträge" neu sortieren (4 Zeilen verschieben)
  ~ Projects/_PROJECTS.md → Abschnitt neu sortieren
  + People/_People.md     → Konventions-Hinweis ergänzen

Fortfahren? (ja/nein)
```

**Erst nach Bestätigung weitermachen.** Im Routine-Lauf ohne Rückfrage fortfahren und den
Dry-Run-Block in den Report übernehmen.

### Schritt 4 — Reparatur durchführen

Pro unsortierter Liste:

- Betroffene Zeilen **unverändert** in die sortierte Reihenfolge bringen — nur Zeilen umstellen
- Tabellen-Header und Trennzeile nicht anfassen
- Fehlt der Konventions-Hinweis, Blockquote direkt unter der Einleitung ergänzen:
  ```
  > 🔤 Konvention: Listen-Einträge in dieser Datei immer alphabetisch sortieren — neue Einträge an der korrekten Position einfügen, nicht unten anhängen.
  ```

#### Sicherheitsprüfung nach jedem Schreibvorgang

⚠️ **Beim Umsortieren wird die Datei komplett neu geschrieben — dem Diff ist dabei nicht zu trauen.**
Ein Umsortier-Diff zeigt fast jede Zeile als verschoben; ob dabei eine verlorenging, sieht man darin
nicht. Belegen lässt es sich nur mengenmäßig: alle nicht-leeren Zeilen beider Fassungen sortiert
vergleichen, die linke Seite muss leer sein.

```bash
grep -v '^[[:space:]]*$' alt.md | sort > /tmp/a
grep -v '^[[:space:]]*$' neu.md | sort > /tmp/b
comm -23 /tmp/a /tmp/b        # leer = keine Zeile verloren
```

Dazu die Einträge zählen (vorher wie nachher dieselbe Zahl).

⚠️ Die Gegenprobe gegen `git show HEAD:<datei>` misst etwas **anderes** — sie zeigt auch alles, was
vor der eigenen Arbeit uncommittet im Arbeitsbaum lag. Für „ist etwas verlorengegangen?" ist die
Vorher-Fassung **aus dem Arbeitsbaum** die richtige Seite.

```
Zusätzlich gegenlesen:
  □ Gleiche Anzahl Zeilen/Einträge wie vorher?
  □ Reihenfolge jetzt korrekt?
Bei Abweichung: Datei als „⚠ Patch-Fehler" melden, nicht weiterpatchen
```

### Schritt 5 — Report speichern

`Brain/Maintenance/alphabetic-index-repair-report.md` **komplett überschreiben**:

```markdown
---
last-run: YYYY-MM-DD
status: ok | action-required
tags: [maintenance]
created: YYYY-MM-DD
---

# Alphabetic Index Repair — YYYY-MM-DD

Hub-Dateien geprüft: N
Bereits korrekt: N
Listen neu sortiert: N
Hinweise ergänzt: N
Fehler: N

[Alle Hub-Listen alphabetisch sortiert ✓]
```

**`status`-Regel:** `ok` wenn Fehler = 0, sonst `action-required`.

---

## Regeln

- **Nur Zeilen umstellen** — niemals Zeileninhalt, Spalten oder Formatierung ändern
- **Niemals** Einträge löschen, zusammenführen oder zwischen Listen verschieben
- Frontmatter und Prosa-Abschnitte der Hub-Dateien nicht anfassen
- Erst Dry Run, dann Bestätigung (außer im Routine-Lauf)
- Nach jedem Schreibvorgang die Zeilen-Vollständigkeit mengenmäßig verifizieren
- `Daily/_Daily.md` und die Monats-Hubs: chronologisch absteigend prüfen, nicht alphabetisch

---

## Routine

Sinnvolle Platzierung: **nach** den inhaltsverändernden Läufen (redundancy-check, frontmatter-repair),
damit neu erzeugte Hub-Einträge mitsortiert werden, und **vor** den Read-only-Reports
(broken-links, orphan-check).

---

## Aufruf

> „Führe obsidian-alphabetic-index-repair aus"
> „Sortier die Hub-Dateien"
> „Sind die Hub-Listen noch alphabetisch?"
