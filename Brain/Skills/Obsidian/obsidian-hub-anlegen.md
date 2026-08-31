---
tags: [skill, obsidian, hub]
created: 2026-01-01
status: active
---
# Skill: obsidian-hub-anlegen

Legt neue Hub-Dateien (`_`-Präfix) und die zugehörigen Verzeichnisse an — nach festem Template,
damit jeder Hub die gleiche Struktur hat und die Sortier-Konvention von Anfang an drinsteht.

Verwandt: [[obsidian-alphabetic-index-repair]] (prüft und repariert bestehende Hubs).

---

## Wann einen Hub anlegen?

- Ein **neues Verzeichnis** unter `Brain/` entsteht (neuer Themenbereich, neue Projekt-Kategorie)
- Ein bestehender Bereich sammelt so viele Dateien, dass eine Übersicht nötig wird
- **Regel: Kein Verzeichnis ohne Hub.** Jedes Verzeichnis bekommt eine `_<Name>.md` als Einstiegspunkt.

---

## Namenskonvention

- Dateiname: `_` + Verzeichnisname
- **Hubs der festen Bereichsstruktur schreiben den Namen in Großbuchstaben** — `_PROJECTS`, `_SKILLS`, `_DAILY`, `_DECISIONS`, `_PEOPLE`. Das gilt **auch für deren Unterordner**: `Plans/Archiv/_ARCHIV.md`, `Skills/Obsidian/_OBSIDIAN.md`. Keine Ausnahme, keine Tiefen-Regel — wer zur mitgelieferten Struktur gehört, ist CAPS.
- **Hubs von Ordnern, die du selbst für ein Projekt oder Thema anlegst, übernehmen den Verzeichnisnamen unverändert** — `Projects/WebApp/_WebApp.md`, `Projects/PartnerAPI/_PartnerAPI.md`. Ein Projektname in Großbuchstaben wäre unlesbar und würde ihn vom Repo-/Ordnernamen entkoppeln.
- Das `_`-Präfix sorgt dafür, dass der Hub in Obsidian oben im Ordner steht und von den Wartungs-Skills als Meta-Datei erkannt wird

> Die Trennlinie ist „mitgeliefert vs. selbst angelegt", nicht „oben vs. unten". Eine Regel nach
> Verschachtelungstiefe hatte genau eine Ausnahme (`_ARCHIV` liegt unter `Plans/`) — und eine Regel
> mit einer Ausnahme wird nicht befolgt, sondern nachgeschlagen.

---

## Template

```markdown
---
tags: [hub, <bereich>]
created: YYYY-MM-DD
---
# <Name>

> <Ein Satz: Was liegt in diesem Bereich, was gehört NICHT hierher (→ Verweis).>

> 🔤 Konvention: Listen-Einträge in dieser Datei immer alphabetisch sortieren — neue Einträge an der korrekten Position einfügen, nicht unten anhängen.

## <Einträge | Projekte | Skills | …>

| <Datei> | <Inhalt / Wann verwenden / Status> |
|---|---|
| [[ErsterEintrag]] | … |
```

**Pflicht in jedem neuen Hub:**

1. Frontmatter mit `tags` (mindestens `hub`) und `created`
2. Einleitungs-Blockquote — ein Satz Zweck und Abgrenzung
3. **🔤 Konventions-Hinweis** (Wortlaut exakt wie oben) direkt unter der Einleitung — bei chronologischen Hubs stattdessen die 📅-Variante
4. Mindestens eine Liste oder Tabelle mit Wikilinks auf die Dateien des Bereichs — von Anfang an alphabetisch

---

## Ablauf

### Schritt 1 — Prüfen, ob der Hub schon existiert

```
Glob "Brain/**/_*.md" → gibt es bereits einen Hub für den Bereich?
```

Existiert er schon → nichts anlegen, höchstens erweitern.

### Schritt 2 — Hub-Datei nach Template anlegen

Datei unter `Brain/<Pfad>/_<Name>.md` schreiben — das Verzeichnis entsteht dabei mit.
Vorhandene Dateien des Bereichs sofort alphabetisch in die Liste eintragen.

### Schritt 3 — Im Eltern-Hub registrieren

Den neuen Hub im übergeordneten Hub verlinken — **an alphabetisch korrekter Position**:

- Unter-Hub (z.B. neues Projekt-Verzeichnis) → Eintrag im Bereichs-Hub (`Projects/_PROJECTS.md`)
- Neuer Top-Level-Bereich → Eintrag in `_INDEX.md` unter „Bereiche in diesem Vault" **und** im Navigations-Hinweis-Block

### Schritt 4 — Verifizieren

```
Neuen Hub gegenlesen:
  □ Frontmatter korrekt (tags als echtes YAML-Array, nicht als String)?
  □ 🔤-Hinweis vorhanden?
  □ Liste alphabetisch?
Eltern-Hub: neuer Eintrag an der richtigen Position?
```

---

## Regeln

- **Niemals** einen Hub ohne den 🔤 Konventions-Hinweis anlegen
- Kein Verzeichnis ohne Hub-Datei
- Neue Einträge in Eltern-Hubs immer alphabetisch einsortieren, nie anhängen
- Bestehende Hubs nicht per Template überschreiben — dieser Skill legt nur **neue** an
- Chronologische Hubs (Logs) bekommen die 📅-Variante des Hinweises statt der 🔤-Variante

---

## Aufruf

> „Leg einen neuen Hub für X an"
> „Erstell ein neues Verzeichnis für X im Brain"
> „Neuer Bereich X in Brain"
