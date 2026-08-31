---
tags: [skill, obsidian, daily]
created: 2026-01-01
status: active
---
# Skill: obsidian-daily-todos-verschieben

## Zweck

Wenn der Nutzer sagt *„verschiebe die TODOs auf morgen"* (typisch abends) oder morgens sinngemäß
*„übernimm die offenen TODOs von gestern"* — offene TODOs aus der jüngsten Tagesdatei in die nächste
übertragen, damit sich offene Punkte nicht in alten Tagesdateien sammeln.

## Ablauf

1. **Die Konventionen von [[obsidian-daily-note]] gelten** (Pfad `Daily/YYYY-MM/YYYY-MM-DD.md`, Template, kein Datums-Titel, keine Wikilinks, Themen als H1 mit `---` dazwischen, Einzeleinträge als H2). Datum vorher messen. Bei Monatswechsel: Monatsordner + Monats-Hub anlegen und in `_Daily.md` verlinken.

2. **Quelle lesen:** die jüngste Tagesdatei mit offenen `- [ ]`-Punkten im TODO-Abschnitt.

3. **Ziel anlegen** (falls nicht vorhanden): Tagesdatei für den **nächsten Arbeitstag** — nicht wörtlich „morgen". Der Freitagslauf zielt auf Montag; eine Samstags- oder Sonntagsdatei wird nie geöffnet. Erster Abschnitt: `# TODO (übertragen aus der Daily YYYY-MM-DD)`.

4. **TODOs auf aktuellem Stand übertragen, nicht blind kopieren:** überholte „nächste Schritte" korrigieren, Teil-Erledigtes zusammenfassen — nur der offene Rest wandert; was am Quelltag passiert ist, bleibt dort als Inhalts-Abschnitt dokumentiert.

   **Auf einen Verweis kürzen, sobald es zum Punkt eine maßgebliche Datei gibt** (Repo-Checkliste,
   Plan-Notiz, Projektnotiz): ein Satz, was als Nächstes ansteht, plus Pfad — nicht der ganze
   Kontextabsatz. Ein ausformulierter TODO-Punkt neben einer Checkliste ist eine Zweitfassung und
   läuft ihr über kurz oder lang davon; der Kontext lebt in der maßgeblichen Datei, das TODO sagt
   nur, dass und womit heute weiterzumachen ist.

   Muster:
   ```markdown
   - [ ] **WebApp: Abschnitt 1 der E2E-Abnahme fahren** → Repo-Datei `Plans/WebApp_E2E_Checkliste.md` (maßgeblich)
   ```

   Neues, das noch nirgends steht, gehört **vor** dem Kürzen erst in die maßgebliche Datei —
   gekürzt wird der Übertrag, nicht die Information.

5. **Übertragene Punkte aus der Quelle löschen** — kein „verschoben"-Marker, keine durchgestrichenen Reste.

6. **Erledigte Punkte ebenfalls löschen.** Der TODO-Abschnitt führt nur, was offen ist; bleibt nichts offen, verschwindet die ganze `# TODO`-Überschrift samt Trennlinie. **Vorher prüfen:** Steht das Ergebnis des erledigten Punkts schon in einem Inhalts-Thema des Tages oder in der Zielnotiz? Wenn nicht, erst dorthin schreiben, dann den Punkt löschen — **gelöscht wird der Haken, nicht die Information.**

7. **Monats-Hub sofort mitziehen:** Zeile für den neuen Tag oben in die Index-Tabelle. ⚠️ Nie auslassen — eine vorab angelegte Tagesdatei ohne Hub-Zeile ist genau der Befund, den [[obsidian-orphan-check]] am Folgetag meldet.

## Abgrenzung

- Inhaltliche Tageseinträge schreibt [[obsidian-daily-note]].
- Dauerhaft Unerledigtes, das kein Tages-TODO mehr ist, gehört in die Projekt-TODO-Liste, nicht ewig weitergereicht — **beim dritten Übertrag prüfen, ob es dorthin gehört**.

## Aufruf

> „Verschiebe die TODOs auf morgen"
> „Übernimm die offenen TODOs von gestern"
