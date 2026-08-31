---
tags: [skill, obsidian, maintenance]
created: 2026-01-01
status: active
---
# Skill: obsidian-orphan-check

Findet Markdown-Dateien in `Brain/`, die nicht korrekt im Graph verankert sind — zwei
Prüfungen:

1. **Waisen:** Dateien, auf die kein einziger `[[Wikilink]]` zeigt (0 Backlinks)
2. **Fehlender Hub-Eintrag:** Dateien mit Backlinks, die aber **nicht in ihrer zuständigen Hub-Datei** verlinkt sind (Konvention: keine Datei ohne Hub-Eintrag)

Standardmäßig nur Report — Hub-Einträge ergänzen nur nach Bestätigung (Schritt 5).

---

## Wie es funktioniert

**Prüfung 1 — Waise:** 0 Backlinks. Schwellenwert: ein Backlink reicht. **Hub-Dateien zählen als
Backlink.** Eine Datei, die nur über ihren Hub verlinkt ist, ist keine Waise — der Hub ist ihr
legitimer Ankerpunkt.

**Prüfung 2 — Hub-Eintrag:** Zuständiger Hub = `_*.md` im selben Verzeichnis; existiert dort
keiner, der Hub des nächsthöheren Verzeichnisses (Fallback `_INDEX.md`). Die Datei besteht die
Prüfung, wenn dieser Hub unter ihren Backlinks ist. Eine Datei kann Prüfung 1 bestehen und an
Prüfung 2 scheitern — z.B. wenn sie nur aus einer Tagesdatei heraus verlinkt ist.

**Sonderfall `Daily/`:** Tagesdateien liegen in Monatsordnern und gelten als eingetragen, wenn sie
in der Index-Tabelle **ihres Monats-Hubs** (`Daily/YYYY-MM/_YYYY-MM.md`) stehen. Die Monats-Hubs
selbst müssen in `Daily/_Daily.md` verlinkt sein (normale Unter-Hub-Prüfung).

### Der sparsame Weg: ein lokaler Graph-Scan

Ein API-Weg kostet pro Datei einen Aufruf. Billiger und mit gleicher Semantik: **ein Durchlauf über
alle `.md` unter `Brain/`** im lokalen Repo — Dateiliste und Wikilinks selbst einlesen, die
Backlinks daraus aufbauen (analog [[obsidian-broken-links-report]]).

Nötige Gegenmaßnahmen bei eigener Link-Extraktion:

- **Alias-Split auch in der escapten Tabellenform** (`[[Pfad/_Hub\|Alias]]`) plus `TrimEnd '\','/'`. Ohne das werden korrekt verlinkte Unter-Hubs falsch als „ohne Hub-Eintrag" gemeldet — genau die stehen in Eltern-Hub-Tabellen mit Pfad + Alias.
- **Code-Fences und Inline-Code vor der Extraktion entfernen** — sonst zählen dokumentierte Beispiel-Links als echte Backlinks.
- **Vorher `\r` aus dem Text entfernen.** Bei CRLF-Zeilenenden scheitert das `$`-Ende der Fence-Regex, es wird **kein einziger** Code-Block entfernt, und alle Beispiel-Links zählen mit. Fällt nur auf, wenn ein Beispiel-Link ins Leere zeigt — sonst still falsche Backlinks.
- **Hub-Auflösung über den echten Dateinamen**, nicht über den Anzeigenamen des Links.

---

## Scope

- Geprüft: `Brain/` rekursiv, alle `.md`
- **Ausgenommen** (werden bewusst nicht verlinkt):
  - Dateien mit `_`-Präfix — Hub-/Meta-Dateien
  - `Brain/Maintenance/*.md` — Reports, keine Wissensdokumente
  - Dateien im Vault-Root (`CLAUDE.md`, `AGENTS.md`, `README.md`) — werden direkt per Pfad gelesen

---

## Ablauf

### Schritt 0 — Datum messen

Systemzeit abfragen, bevor irgendetwas geschrieben wird. Gilt für `last-run`.

### Schritt 1 — Dateien einsammeln

Alle `.md` unter `Brain/` rekursiv, `_`-Dateien und `Maintenance/` herausfiltern.

### Schritt 2 — Backlinks aufbauen und prüfen

```
Für jede Datei:
  Backlinks = 0                             → Waise merken
  sonst: zuständigen Hub bestimmen
    Hub nicht unter den Backlinks           → „ohne Hub-Eintrag" merken
```

### Schritt 3 — Report ausgeben

```
ORPHAN CHECK — ERGEBNIS
────────────────────────────────────
Dateien geprüft: N
Verwaiste Dateien: X
Ohne Hub-Eintrag: Y

Waisen (0 Backlinks):
  Brain/Skills/irgendwas.md
Ohne Hub-Eintrag (Backlinks vorhanden, aber nicht im Hub):
  Brain/Projects/XY/vergessen.md   → gehört in Projects/XY/_XY.md

Keine Änderungen vorgenommen.
```

### Schritt 4 — Report speichern

`Brain/Maintenance/orphan-check-report.md` **komplett überschreiben**:

```markdown
---
last-run: YYYY-MM-DD
status: ok | action-required
tags: [maintenance]
created: YYYY-MM-DD
---

# Orphan Check — YYYY-MM-DD

Dateien geprüft: N
Verwaiste Dateien: N
Ohne Hub-Eintrag: N

[Keine verwaisten Dateien, alle Dateien im Hub verlinkt ✓]

[ODER:]
Waisen:
  Brain/Skills/irgendwas.md
Ohne Hub-Eintrag:
  Brain/Projects/XY/vergessen.md   → Projects/XY/_XY.md
```

**`status`-Regel:** `ok` wenn Waisen = 0 **und** ohne Hub-Eintrag = 0, sonst `action-required`.

**Fundstellen als Pfade, nie als Wikilink** — auch in erklärender Prosa Wikilinks in Inline-Code
setzen (`` `[[Ziel]]` ``). Ein nackter Link im Report erzeugt einen echten Backlink und verfälscht
genau die Zahl, die dieser Skill misst.

**Frontmatter-Regeln:** `tags: [maintenance]` als echtes YAML-Array; `created` aus dem alten Report
übernehmen; komplettes Frontmatter im Schreib-Payload.

### Schritt 5 — Reparatur (optional, nur nach Bestätigung)

Für Funde aus Prüfung 2:

```
Pro Datei:
  → Kurzbeschreibung aus Frontmatter/erster Überschrift ableiten
  → Eintrag im zuständigen Hub an ALPHABETISCH korrekter Position einfügen
    (Format an die bestehende Tabelle des Hubs anpassen)
  → Gegenlesen: Eintrag da, Rest unverändert?
```

Waisen (Prüfung 1) werden **nie** automatisch repariert — dort entscheidet der Nutzer, ob
verlinken, verschieben oder löschen. Nach einer Reparatur den Report aktualisieren.

---

## Regeln

- **Standardmäßig nur lesen** — geschrieben wird nur die Report-Datei; Hub-Einträge ausschließlich in Schritt 5 nach ausdrücklicher Bestätigung
- `_`-Dateien werden **nicht** auf Backlinks geprüft — sie werden von oben verlinkt, nicht von unten. Ausnahme: Unter-Hubs müssen im Eltern-Hub stehen; das deckt Prüfung 2 mit ab (zuständiger Hub eines Unter-Hubs = Hub des Eltern-Verzeichnisses)
- `Maintenance/`-Dateien werden **nicht** geprüft
- Bei Lesefehler: Datei überspringen, als `⚠ Lesefehler` melden, `status: action-required`
- Eine Waise ist **kein Fehler** — nur ein Hinweis
- Hub-Einträge bei Reparatur immer alphabetisch einsortieren

---

## Aufruf

> „Führe obsidian-orphan-check aus"
> „Gibt es verwaiste Dateien im Vault?"
> „Fehlen Hub-Einträge?"
