# AGENTS.md — Betriebsanleitung für diesen Vault

Dieser Ordner ist ein **Second Brain**: das persistente Gedächtnis über Sessions hinweg. Er ist
reines Markdown, jeder Agent mit Dateizugriff kann damit arbeiten.

> 📖 **Die vollständigen Regeln stehen in [`CLAUDE.md`](CLAUDE.md) — lies sie zuerst.** Diese Datei
> ist die Kurzfassung für Agenten, die `CLAUDE.md` nicht automatisch laden. Bei Abweichungen gilt
> `CLAUDE.md`; Regeln werden dort gepflegt, nicht hier doppelt.

## Das Nötigste

**Einstieg:** `Brain/_INDEX.md` → Bereichs-Hub (`_*.md`) → Zieldatei. Wer der Nutzer ist,
steht in `Brain/Me.md`.

**Wohin was gehört:**

| Frage | Ziel |
|---|---|
| Was ist X für ein Projekt? | `Projects/_PROJECTS.md` |
| Warum wurde X so gemacht? | `Decisions/_Decisions.md` |
| Wie mache ich X? | `Skills/_SKILLS.md` — **erst den Skill lesen, dann handeln** |
| Was war zuletzt? | `Daily/_Daily.md` → Monats-Hub → Tagesdatei |
| Was ist X für ein Fremdsystem? | `Knowledge/_KNOWLEDGE.md` |
| Wie ist X eingerichtet? | `Setups/_SETUPS.md` |

**Fünf Regeln, ohne die der Vault zerfällt:**

1. **Keine Datei ohne Hub-Eintrag.** Jede neue Datei sofort im zuständigen `_*.md` verlinken — alphabetisch einsortiert, nicht angehängt.
2. **Frontmatter mit `tags` bei jeder neuen Datei.**
3. **Datum vor jedem Schreibvorgang messen**, nie aus dem Session-Kontext übernehmen — der Fehler ist still.
4. **Vor dem Vollschreiben einer Datei frisch lesen.** Ein Lesestand von vor zwei Stunden ist kein Schreibstand.
5. **Die Daily ist ein Log, kein Wissensspeicher.** Wissen gehört in `Projects/`, `Decisions/`, `Skills/`; der Hergang bleibt im Tag.

## Eigenständig erlaubt

Lesen, neue Dateien anlegen, patchen, Wikilinks setzen, `_INDEX.md` und Tagesdateien aktualisieren.

## Nur nach Rückfrage

Bestehende Inhalte überschreiben oder löschen · Dateien verschieben oder umbenennen ·
größere Strukturänderungen.
