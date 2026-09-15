---
tags: [skill, daily, todos]
created: 2026-01-01
status: active
---

# Skill: Globale TODO-Liste pflegen

> **Ersetzt das Weiterreichen von TODOs durch die Tagesdateien.** TODOs wandern **nicht** von Tag zu
> Tag, und Tagesdateien führen **keinen** `# TODO`-Block mehr. Alles Offene steht in
> `Brain/Daily/TODOs.md`, bis es erledigt ist.

## Zweck

Alles, was mit offenen Punkten zu tun hat: einen neuen aufnehmen, einen erledigten abhaken, die
Liste kurz halten. Auslöser sind Sätze wie *„was ist noch offen?"*, *„nimm das als TODO auf"*,
*„das ist durch"* — oder das Ende einer Arbeit, an deren Anfang ein TODO stand.

## Die drei Dateien

| Datei | Rolle |
|---|---|
| `Brain/Daily/TODOs.md` | **Einzige Quelle offener TODOs.** Projektgruppen (`###`) in Prio-Blöcken (`##`) |
| `Brain/Daily/Archiv/TODOs_Archiv.md` | Erledigtes, dieselben Gruppennamen — aber flach auf `##`, ohne Prio |
| `Brain/Daily/YYYY-MM/YYYY-MM-DD.md` | Was am Tag **passiert** ist — hier steht **nie** ein TODO, weder `- [ ]` noch `- [x]` |

Die Archivdatei und der Hub ihres Ordners entstehen beim ersten Archivieren → [[obsidian-hub-anlegen]].

## ⛔ Die Reihenfolge ist vorgegeben, nicht abgeleitet

Die Gruppen stehen in einer vom Nutzer gesetzten Ordnung und werden **nicht umsortiert** — nicht
nach Fälligkeit, nicht nach Alter, nicht nach Zahl der offenen Punkte:

1. `## Brain-Maintenance (BrainWork)` — **immer ganz oben**; wird auch maschinell befüllt (s. u.)
2. Die Prio-Blöcke mit den Projektgruppen als `###`
3. `## Sonstige` — zuletzt, ohne Prio, weil einzeln zu klein

Die maßgebliche Fassung dieser Ordnung ist der Kopf von `TODOs.md` selbst.

**Was die Stufen bedeuten** — das ist keine reine Rangfolge:

- **Die oberen Blöcke:** aktive Arbeit in der gesetzten Ordnung.
- **Der unterste Prio-Block: reaktiv.** Dort wird nur weitergearbeitet, wenn es einen **Anlass von
  außen** gibt — ⛔ nicht nachfassen, nicht vorarbeiten. ⚠️ Der Anlass muss **nicht vom Kunden
  kommen**: eine intern angekündigte Umstellung ist auch einer. Wer den Punkt als „meldet sich X …"
  formuliert, übersieht genau diese Fälle. **Reaktiv heißt: warten, bis etwas passiert — nicht:
  warten, bis jemand schreibt.**
- ⏳ **`Warten` ist keine Prio, sondern ein Zustand.** Eine Gruppe kann ganz oben stehen und trotzdem
  ruhen, weil eine Antwort fehlt. Das steht als `⏳ Warten`-Callout **an der Gruppe** samt Grund.
  ⛔ Die Gruppe bleibt an ihrer Position — sonst ist nach der Wiederaufnahme nicht mehr erkennbar,
  wie wichtig sie ist. Ein Wartezustand ist **kein** Grund, die Prio zu ändern.
- **Eine Gruppe kann die Anwendung einer anderen sein — dann steht die Grundlage davor.** Liegt in
  einer Gruppe der projektübergreifende Ablauf und in der anderen nur ein Fall davon, gehört die
  allgemeine nach vorn.

### 🗓️ Die Prio wird nicht hier entschieden

Sie kommt von außerhalb des Vaults (Planungsrunde, Ticketsystem, Absprache). Daraus folgt:

- **Die Reihenfolge nie ableiten, schätzen oder rekonstruieren.** Sie steht in `TODOs.md` und sonst
  nirgends, was von hier aus lesbar wäre. Ein Punkt, der drängender aussieht, ist kein Grund — er
  ist genau der Fall, den die Vorgabe verhindern soll.
- **Verschiebungen kommen als ausdrückliche Ansage.** ⚠️ Ein Prio-Wechsel, der sich aus dem
  Gesprächsverlauf zu ergeben *scheint*, ist keiner.
- **Beim Eintragen das Datum der Entscheidung vermerken, nicht das des Eintragens** — sonst ist
  später nicht unterscheidbar, ob eine Reihenfolge beschlossen oder nur nachgetragen wurde.

### ✅ Die Prio ordnet die Liste, sie sperrt die Arbeit nicht

Kommt zu einem Punkt etwas **herein** — eine Antwort, eine gelieferte Datei, ein Testergebnis —,
wird **direkt daran weitergearbeitet**, auch wenn höher priorisierte Punkte offen sind. Der Zweck
ist, ein Projekt **endlich fertig zu bekommen**.

**Warum das kein Widerspruch zur Prio ist:** Die Prio sagt, was anzufangen ist, wenn nichts
Konkretes treibt. Ein Großteil der Punkte in so einer Liste sind aber **Wartepunkte**. Trifft die
erwartete Antwort ein und wird sie nicht aufgegriffen, weil formal etwas anderes höher steht, fängt
die Wartezeit von vorn an: der Absender ist raus aus dem Thema, der Kontext muss neu aufgebaut
werden, und beim nächsten Nachfassen ist der Punkt genau da, wo er vorher war. **Ein
hereinkommender Auslöser ist das knappste Gut** in wartenden Projekten.

**Die Grenzen davon:**

- ⛔ **Es verschiebt die Prio nicht.** Sonst wäre „ich habe daran gearbeitet" ein Weg, sich selbst
  eine Prio zu geben.
- ⛔ **Der Auslöser gilt für seinen eigenen Punkt**, nicht für das ganze Projekt.
- **Es ist kein Grund, den Auslöser zu erfinden.** „Da könnte bald etwas kommen" ist keiner.

## Ablauf

### Neuen Punkt aufnehmen

1. **Erst lesen, dann schreiben:** `git -C <vault> pull`, dann `TODOs.md` — steht der Punkt schon
   da, wird er ergänzt, nicht zweitgefasst.
2. In die passende Projektgruppe (`###`) einsortieren, ohne die Gruppenreihenfolge anzutasten.
3. **Auf einen Verweis kürzen, sobald es zum Punkt eine maßgebliche Datei gibt** (Checkliste, Plan,
   Projektnotiz): ein Satz, was als Nächstes ansteht, plus Pfad — nicht der ganze Kontextabsatz. Ein
   ausformulierter TODO-Punkt neben einer Checkliste ist eine Zweitfassung und läuft ihr über kurz
   oder lang davon. Muster:
   `- [ ] **Abschnitt 1 der Abnahme fahren** → `Plans/<Projekt>_E2E_Checkliste.md``
4. Neues, das noch nirgends steht, gehört **vor** dem Kürzen erst in die maßgebliche Datei —
   gekürzt wird der Eintrag, nicht die Information.

### 🤖 Brain-Maintenance: der einzige Block mit einer schreibenden Routine

Die Abendroutine [[obsidian-daily-commit-push]] (Schritt 1) trägt Maintenance-Befunde selbst hier
ein — **ein Punkt pro Report** mit `status: action-required`:

```markdown
- [ ] **orphan-check YYYY-MM-DD** — 2 Dateien ohne Hub-Eintrag → `Maintenance/orphan-check-report.md`
```

- **Der Punkt trägt das Laufdatum**, nicht das des Eintragens — der Abendlauf schreibt sein eigenes
  Datum, auch wenn der Commit nach Mitternacht durchgeht.
- **Ein wiedergemeldeter Befund aktualisiert den bestehenden Punkt**, er legt keinen zweiten an
  (`— seit YYYY-MM-DD unverändert, dritter Lauf`). Sonst steht nach einer Abwesenheit derselbe Fund
  zehnmal hier.
- **Steht nur der Platzhalter `_(Nichts offen.)_`**, ersetzt der erste Punkt ihn; wird der letzte
  Punkt archiviert, kommt er zurück.
- ⚠️ **Zitierte Wikilink-Ziele gehören in Inline-Code** (`` `[[Ziel]]` ``). Ein Punkt über einen
  kaputten Link erzeugt sonst selbst eine Fundstelle im nächsten Broken-Links-Lauf — und weil diese
  Datei dauerhaft gelesen wird, verschwindet die nicht mit dem Tag.

### Punkt erledigen

1. Auf `- [x]` setzen und mit dem Erledigungsdatum versehen. **Nicht löschen** — der Haken ist hier
   die Information.
2. **Vorher prüfen, ob das Ergebnis inhaltlich irgendwo steht** (Tagesdatei des Erledigungstags,
   Zielnotiz, Plan). Wenn nicht: erst dorthin schreiben. Die TODO-Liste ist keine Wissensablage.
3. Hat eine Gruppe keinen einzigen offenen Punkt mehr, wandert sie ans Ende **ihres Prio-Blocks**,
   nicht ans Dateiende.

### Archivieren

1. **Trigger:** Sind es **mehr als 5** erledigte Punkte — Gesamtzahl über **alle** Gruppen, nicht
   pro Gruppe — wandern die ältesten ins Archiv, bis wieder genau 5 stehen.
2. Nötigenfalls **einzelne Zeilen aus einem `✅ Erledigt (Datum)`-Block herausbrechen**, nicht nur
   ganze Blöcke — die Zahl 5 zählt Punkte, nicht Blöcke.
3. Im Archiv in dieselbe Gruppe einsortieren, neueste zuerst. Existiert dort schon ein Block mit
   demselben Datum, wächst er weiter, statt einen zweiten anzulegen.
4. In beiden Dateien die Fußzeile `**Letzte Aktualisierung:**` mitziehen — mit gemessenem Datum und
   einem Halbsatz, was passiert ist.

## Fallstricke

- ⚠️ **Sortierte Listen und Tabellen nicht per `append` füttern** — der Eintrag landet hinter der
  letzten Zeile, nicht an der richtigen Position. → [[obsidian-vault-patch-fallstricke]]
- ⚠️ Die Liste ist eine geteilte Datei — vor jedem abschnittsersetzenden Schreiben `git pull` und
  den Zielabschnitt frisch lesen.

## Abgrenzung

- Inhaltliche Tageseinträge → [[obsidian-daily-note]] bzw. [[obsidian-brain-update]].
- Projekte mit vielen eigenen TODOs dürfen eine eigene TODO-Datei im Projektordner führen;
  `TODOs.md` trägt dann nur den Verweis darauf.
- Prüfpunkte eines Plans oder einer Checkliste sind **keine** TODOs dieser Liste — sie leben in der
  Plan-Datei. Hier steht höchstens ein Punkt, der auf sie zeigt.
