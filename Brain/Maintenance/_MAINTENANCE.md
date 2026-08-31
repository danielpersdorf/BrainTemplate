---
tags: [maintenance, hub]
created: 2026-01-01
---
# Maintenance

> Automatisch erzeugte Reports der Wartungs-Skills. Sie werden bei jedem Lauf **komplett
> überschrieben** — hier steht immer nur der letzte Stand, die Historie liefert Git.

> ℹ️ Diese Hub-Datei indiziert **nur report-erzeugende** Wartungs-Skills. Skills ohne Report (`obsidian-brain-update`, `obsidian-daily-commit-push`, `obsidian-hub-anlegen`, `obsidian-project-split`, …) stehen im Skill-Hub [[Skills/Obsidian/_OBSIDIAN]].

> 📄 Namenskonvention: Jede Report-Datei heißt `<skill-thema>-report.md` — also `orphan-check-report.md`, nicht `orphan-check.md`. Der Dateiname sagt damit selbst, dass er das **Ergebnis** ist und nicht die Anleitung (die heißt `obsidian-<thema>` in `Skills/Obsidian/`).

> 🔤 Konvention: Listen-Einträge in dieser Datei immer alphabetisch sortieren — neue Einträge an der korrekten Position einfügen, nicht unten anhängen.

## Reports

Die Zeilen entstehen beim jeweils ersten Lauf eines Skills.

| Report | Skill | Status |
|---|---|---|
| `alphabetic-index-repair-report.md` | [[obsidian-alphabetic-index-repair]] | *(noch kein Lauf)* |
| `broken-links-report.md` | [[obsidian-broken-links-report]] | *(noch kein Lauf)* |
| `daily-redundancy-report.md` | [[obsidian-daily-redundancy-check]] | *(noch kein Lauf)* |
| `decision-scan-report.md` | [[obsidian-decision-scan]] | *(noch kein Lauf)* |
| `frontmatter-repair-report.md` | [[obsidian-frontmatter-repair]] | *(noch kein Lauf)* |
| `orphan-check-report.md` | [[obsidian-orphan-check]] | *(noch kein Lauf)* |
| `token-efficiency-report.md` | [[obsidian-token-efficiency-analysis]] | *(noch kein Lauf)* |

## Frontmatter jedes Reports

```yaml
---
last-run: YYYY-MM-DD
status: ok | action-required
tags: [maintenance]
created: YYYY-MM-DD    # Wert des ersten Laufs, wird nie nachgezogen
---
```

## Legende

- `status: ok` — kein Handlungsbedarf
- `status: action-required` — Einträge prüfen und manuell entscheiden

> ⚠ `action-required` ist teuer: die Commit-Routine macht daraus das `⚠ Maintenance`-Flag in der
> Tagesdatei und damit einen Hinweis zu Session-Beginn. Deshalb nur setzen, wenn wirklich etwas zu
> entscheiden ist — Funde, die man bloß wissen sollte, gehören in den Report, nicht ins Flag.
> Ein Flag, das jede Woche dieselben bekannten Kandidaten meldet, nutzt sich ab und wird ignoriert.

> 🔗 **Fundstellen in Reports immer als Pfad oder in Inline-Code** (`` `[[Ziel]]` ``), nie als nackter
> Wikilink. Ein echter Link im Report erzeugt einen Backlink und verfälscht genau die Zahl, die der
> Report misst; zeigt er ins Leere, meldet ihn der nächste Broken-Links-Lauf als Fundstelle.
