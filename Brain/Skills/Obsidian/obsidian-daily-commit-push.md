---
tags: [skill, obsidian, git]
created: 2026-01-01
status: active
---
# Skill: obsidian-daily-commit-push

Täglicher Git-Commit des Vaults — und davor der **Maintenance-Befund** in die globale TODO-Liste,
wenn ein Wartungslauf etwas Auffälliges gemeldet hat. Läuft als letzte Routine des Tages, nach den
Maintenance-Skills, damit deren Reports mit im Commit landen.

---

## Schritt 0 — Datum messen

Systemzeit abfragen. Der Eintrag trägt dieses Datum.

## Schritt 1 — Maintenance-Befunde in die TODO-Liste

Alle Report-Dateien unter `Brain/Maintenance/` auf ihr `status`-Frontmatter prüfen:

```powershell
Get-ChildItem "$vault\Brain\Maintenance" -Filter *.md |
  Select-String -Pattern '^status:\s*action-required' -List
```

- **Keine Treffer** → nichts tun. Grüne Tage bleiben rauschfrei — kein „lief durch"-Eintrag, das steht im Report-Frontmatter.
- **Mindestens ein `action-required`** → **pro betroffenem Report ein eigener Punkt** unter `## Brain-Maintenance (BrainWork)`, dem ersten Block von `Brain/Daily/TODOs.md`. Steht dort nur der Platzhalter `_(Nichts offen.)_`, ersetzt der erste Punkt ihn.

```markdown
## Brain-Maintenance (BrainWork)

- [ ] **orphan-check YYYY-MM-DD** — 2 Dateien ohne Hub-Eintrag → `Maintenance/orphan-check-report.md`
- [ ] **token-efficiency YYYY-MM-DD** — 1 Split-Kandidat → `Maintenance/token-efficiency-report.md`
```

### Regeln für den Eintrag

- **Ein Punkt pro Report, nicht pro Lauf.** Die Reports werden einzeln erledigt — ein gebündelter Punkt ließe sich nur ganz oder gar nicht abhaken, und Teilerledigung müsste in den Fließtext.
- **Form: ein Satz plus Pfad**, wie für jeden TODO-Punkt ([[obsidian-todos-liste-pflegen]]) — fett der Report samt Laufdatum, dann der Kurzgrund, dann der Report-Pfad. Der Kontext lebt im Report, nicht im Punkt.
- **Nur Anomalien melden, nie den Normalzustand** — es ist ein Aufmerksamkeits-Signal, kein Protokoll.
- Kurzgrund aus dem jeweiligen Report ziehen (die Kopfzeile genügt).
- **Der Punkt trägt das Datum seines Laufs, nicht das des Eintragens.** Der Abendlauf schreibt sein eigenes Datum, auch wenn der Commit nach Mitternacht durchgeht. Ohne diese Trennung ist ein Befund später keinem Lauf mehr zuzuordnen.
- **Meldet ein Report denselben Befund erneut, den bestehenden Punkt aktualisieren — keinen zweiten anlegen.** Ein unverändert offener Fund erzeugt sonst pro Abendlauf eine Zeile; nach einer Abwesenheit stehen zehn Zeilen für einen Befund. Zielform ist ein Punkt mit Verlauf: `— seit YYYY-MM-DD unverändert, dritter Lauf`.
- **Punkt und Report müssen dasselbe sagen.** Ein Report, der in der Liste steht, muss selbst `status: action-required` und den Fund tragen — der Eintrag wird aus dem Frontmatter abgeleitet, nie aus Wissen, das nur im Lauf vorliegt. Fällt beim Eintragen auf, dass ein Report nicht nachgezogen wurde: erst den Report schreiben, dann eintragen.
- **Erledigen nach den Regeln der TODO-Liste:** auf `- [x]` setzen mit Erledigungsdatum und einem Halbsatz, was getan wurde — nicht löschen. Ab dem sechsten erledigten Punkt der Gesamtliste wandern die ältesten ins Archiv ([[obsidian-todos-liste-pflegen]]).
- ⚠️ **Backticks um zitierte Link-Ziele erhalten.** Nennt der Punkt ein kaputtes Wikilink-Ziel, steht es als Inline-Code (`` `[[Ziel]]` ``) — genau wie im Report selbst. Ohne Backticks erzeugt die TODO-Liste beim nächsten Broken-Links-Lauf selbst eine Fundstelle, und der Befund sieht wie neu aus. Die Falle ist hier **größer** als in einer Tagesdatei: `TODOs.md` wird dauerhaft gelesen, kein abgeschlossener Tag.
- ⚠️ **`TODOs.md` ist eine geteilte Datei** — vor dem Schreiben `git pull` und den Zielabschnitt frisch lesen, nicht ans Dateiende hängen.

> 💡 **Warum die TODO-Liste und nicht die Tagesdatei?** Ein Befund ist ein **Arbeitsauftrag**, und
> Arbeitsaufträge gehören dorthin, wo alle anderen stehen. Schreibt man ihn in eine Tagesdatei, muss
> die Regel erst dafür sorgen, dass es die Zieldatei überhaupt gibt: nächster Arbeitstag statt
> „morgen", notfalls Tagesdatei anlegen, Monats-Hub-Zeile nachtragen, Flags einer Abwesenheit
> bündeln. ⚠️ Daraus entsteht ein Fehlermodus, der sich **selbst füttert**: jede nur für ihren
> Befund angelegte Tagesdatei ist am Folgeabend ein neuer Orphan-Check-Befund („Tagesdatei ohne
> Hub-Eintrag"), weshalb derselbe Fund über eine Abwesenheit hinweg wächst und Lauf um Lauf gemeldet
> wird. Mit `TODOs.md` fällt das alles weg — die Datei existiert immer und hat ihren Hub-Eintrag.
> Die Positionsregel für den Daily-Kopf, falls ein Vault noch Bestand aus dem alten Modell trägt:
> [[obsidian-maintenance-flag-position]].

## Schritt 2 — Pull

```powershell
git -C $vault pull origin main
```

Bei Fehler: abbrechen und die Meldung ausgeben. Nicht mit lokalem Stand weiterarbeiten.

## Schritt 3 — Änderungen prüfen

```powershell
git -C $vault status --short
```

Keine Änderungen → beenden mit „Keine Änderungen — kein Commit nötig."

## Schritt 4 — Diff ansehen

```powershell
git -C $vault diff --stat HEAD
git -C $vault diff HEAD -- "*.md" | Select-Object -First 150
```

Welche Notizen, welche Projekte, was wurde dokumentiert? Daraus entsteht die Commit-Nachricht.

## Schritt 5 — Stagen

```powershell
git -C $vault add -A
```

> Das ist die **einzige** Stelle, an der ein pauschales `add -A` im Vault richtig ist: der
> Sammel-Commit soll bewusst alles mitnehmen. Bei jedem Zwischen-Commit während der Arbeit gilt das
> Gegenteil — dort nur die eigenen Zieldateien einzeln stagen (→ [[_OBSIDIAN]]).

## Schritt 6 — Commit-Nachricht

Format `brain: <kurze Beschreibung>`, z.B.:

- `brain: WebApp Deploy-Flow dokumentiert`
- `brain: Daily 2026-06-18 aktualisiert`
- `brain: 3 Notizen bearbeitet (Projects, Decisions, Daily)`

⚠️ Nachricht in eine Datei schreiben und `git commit -F` nehmen, nicht `-m` mit Here-String — und
die Nachrichtendatei **ohne BOM** schreiben. → [[_OBSIDIAN]]

## Schritt 7 — Commit und Push

```powershell
git -C $vault commit -F $msgFile
git -C $vault push origin main
```

---

## Abschluss-Ausgabe

Commit-Hash und Nachricht · Push-Status · Anzahl geänderter Dateien · ob ein Maintenance-Punkt in
`Daily/TODOs.md` eingetragen wurde (welcher Report, welcher Kurzgrund).
