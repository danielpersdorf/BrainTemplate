---
tags: [skill, obsidian, maintenance]
created: 2026-01-01
status: active
---
# Skill: obsidian-broken-links-report

Scannt alle Markdown-Dateien in `Brain/` und prüft, ob die Zieldateien ihrer
`[[Wikilinks]]` tatsächlich existieren. Gibt einen Report aus — ändert **nichts**.

---

## Wie es funktioniert

Der Scan läuft **lokal über die Dateien**: Dateiliste und Wikilinks selbst einlesen. Bei ein paar
hundert Dateien spart das gegenüber ebenso vielen Einzelaufrufen eines API-Werkzeugs den Großteil
der Tokens.

> 🛑 **Kein Backlink-/Link-Array einer API verwenden, um kaputte Links zu finden.** Solche Arrays
> enthalten in aller Regel nur die **aufgelösten** Links — kaputte fehlen darin einfach, und der
> Report meldet systematisch 0. Ein Lauf, der auf diesem Weg entstanden ist, ist nicht belastbar.

---

## Ablauf

### Schritt 1 — Auflösungs-Referenz aufbauen

Alle Dateien des **ganzen Vaults** rekursiv sammeln (ohne `.git`, `.obsidian`, `.trash`) →
Pfad-Set + Basename-Set.

Bewusst der ganze Vault, nicht nur `Brain/`: Obsidian löst Links vault-weit auf, und Links
nach außen (z.B. `[[README]]`) gelten als OK.

### Schritt 2 — Pro Datei in `Brain/` die Links prüfen

Wikilinks selbst extrahieren: `!?\[\[([^\[\]]+?)\]\]` — Embeds `![[…]]` mitzählen. Vorher
**Codeblöcke und Inline-Code entfernen**, sonst zählen dokumentierte Beispiel-Links aus
Skill-Dateien als kaputt.

Pro Link-Text in dieser Reihenfolge normalisieren:

1. `\|` → `|` (escaped Alias-Trenner in Tabellen, siehe Stolperfalle unten)
2. Alles ab `#` abschneiden (Anchor)
3. Alles ab `|` abschneiden (Alias)
4. Trimmen, `\` → `/`, Slash am Ende weg
5. Leerer Rest → OK (reiner Anchor-Link auf sich selbst)

Der Link gilt als **aufgelöst**, wenn für den Rest *oder* Rest + `.md` eine dieser vier
Obsidian-Regeln greift:

| Regel | Beispiel |
|---|---|
| exakter Vault-Pfad | `[[Brain/Me]]` |
| relativ zum Ordner der Quelldatei | `[[Agents/_AGENTS]]` in `Brain/_INDEX.md` |
| Teilpfad als **Suffix** eines Vault-Pfads | `[[Skills/deploy]]` von irgendwo |
| Basename vault-weit eindeutig | `[[WebApp]]` |

Greift keine → kaputten Link merken: `{ datei, link }`. Vergleiche case-insensitiv, pro Datei jeden
Link-Text nur einmal prüfen.

### Stolperfalle: escaped Pipes in Tabellen-Wikilinks

Wikilinks **innerhalb von Markdown-Tabellen** escapen den Alias-Trenner: `[[Pfad/_Hub\|Alias]]`.
Wer nur bis zum `|` schneidet, behält ein `\` am Ende und findet die Zieldatei nicht →
falsch-positive Meldungen, typischerweise bei Unter-Hubs. Deshalb Schritt 1 der Normalisierung
**vor** dem Alias-Schnitt. Dieselbe Falle wie in [[obsidian-orphan-check]].

### Stolperfalle: `\r` bei CRLF-Zeilenenden

Beim Entfernen der Code-Fences matcht `$` im Multiline-Modus vor dem `\n`; bei CRLF bleibt das `\r`
stehen, und die schließende Fence-Zeile passt nicht mehr auf ein Muster wie
`^[ \t]*<fence>[ \t]*$` → **kein einziger** Code-Block wird entfernt, alle Beispiel-Links zählen mit.
**Vorher `\r` aus dem Text entfernen.**

> Aus demselben Grund steht hier `<fence>` statt der drei Backticks: ein literales Fence-Zeichen in
> Inline-Code bringt die Fence-Paarung des Resolvers durcheinander, und der Beispiel-Link im
> Report-Template unten wird fälschlich als kaputt gemeldet.

### Wenn 0 kaputte Links herauskommen — gegenprüfen

Ein zu laxer Resolver meldet immer 0. Vor dem Report deshalb einmal:

- **Selbsttest:** Testdatei außerhalb des Vaults mit je zwei gültigen und zwei erfundenen Links (`[[GibtEsNichtXYZ]]`, `[[Plans/NichtVorhanden]]`) plus einem Link in Inline-Code. Erwartung: genau 2 Treffer.
- **Stichprobe:** 3–5 Link-Ziele aus dem Vault per Dateisuche bestätigen.

### Schritt 3 — Report ausgeben

```
BROKEN LINKS REPORT
────────────────────────────────────
Dateien geprüft: N
Dateien mit kaputten Links: X
Kaputte Links gesamt: Y

Brain/Projects/_PROJECTS.md
  → [[Projects/AltesProjekt]]  (Ziel nicht gefunden)

Keine Änderungen vorgenommen.
```

---

## Report speichern

`Brain/Maintenance/broken-links-report.md` **komplett überschreiben**:

```markdown
---
last-run: YYYY-MM-DD
status: ok | action-required
tags: [maintenance]
created: YYYY-MM-DD
---

# Broken Links Report — YYYY-MM-DD

Dateien geprüft: N
Kaputte Links: N

[Alle Links OK ✓]

[ODER:]
Pfad/zur/Datei.md
  → `[[LinkZiel]]`  (Ziel nicht gefunden)
```

**Kaputte Links in der Report-Datei immer in Inline-Code setzen** (`` `[[Ziel]]` ``), nie als
nackten Wikilink. Sonst enthält der Report selbst kaputte Links und meldet sich beim nächsten Lauf
als Fundstelle — der Resolver strippt Inline-Code, damit fällt das Zitat korrekt raus. Für die
Bildschirmausgabe egal, sie landet nicht im Vault.

**`status`-Regel:** `ok` wenn kaputte Links = 0, sonst `action-required`.

**Frontmatter-Regeln:** `tags: [maintenance]` als echtes YAML-Array; `created` aus dem alten Report
übernehmen, nur beim ersten Lauf das heutige Datum; komplettes Frontmatter im Schreib-Payload —
nichts nachträglich patchen.

---

## Regeln

- **Nur lesen, nie schreiben** — außer der Report-Datei
- `_`-Dateien werden **mitgeprüft** (ihre Links müssen stimmen)
- Links auf Dateien außerhalb von `Brain/` gelten als **OK**
- Bei Lesefehler: Datei überspringen, im Report als `⚠ Lesefehler` markieren
- Anchor-Links (`[[Datei#Überschrift]]`) prüft dieser Skill **nicht** — der Anchor wird abgeschnitten. Ein falscher Anchor wird also **nicht** gemeldet; bei Umzügen zeichengenau von Hand gegenprüfen.

---

## Aufruf

> „Führe obsidian-broken-links-report aus"
> „Gibt es kaputte Links im Vault?"
> „Welche Wikilinks zeigen ins Leere?"
