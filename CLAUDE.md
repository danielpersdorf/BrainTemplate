# Claude Brain — Navigations- und Schreibregeln

Dieser Vault ist das persistente Gedächtnis für Sessions. Die Regeln hier gelten für jeden Zugriff
darauf — sie sind der Grund, warum der Vault nach hundert Sessions noch navigierbar ist.

## Sprache

Immer Deutsch antworten, auch wenn Code auf Englisch ist.
*(Anpassen oder streichen, wenn du in einer anderen Sprache arbeitest.)*

## Navigations-Strategie

1. **Immer zuerst:** [[Brain/_INDEX]] lesen
2. **Wer ist der Nutzer?** → [[Brain/Me]]
3. **Projekt-Fragen** → `Brain/Projects/_PROJECTS.md` → relevanten Abschnitt lesen
4. **„Warum wurde X so gemacht?"** → `Brain/Decisions/_DECISIONS.md`
5. **„Was war zuletzt?"** → `Brain/Daily/_DAILY.md` → Monats-Hub → Tagesdatei
6. **„Wie mache ich X?"** → `Brain/Skills/_SKILLS.md` — **erst den Skill lesen, dann handeln**, auch wenn die Aufgabe trivial wirkt
7. **Unbekannte Begriffe** → Volltextsuche über den Vault
8. **Maintenance-Flag:** Enthält die jüngste Tagesdatei eine `⚠ Maintenance`-Zeile, zu Session-Beginn kurz darauf hinweisen (welcher Report `action-required` ist). Ein `✅` davor heißt erledigt; **keine** Flag-Zeile heißt „alle Reports waren `ok`", nicht „wurde gekürzt".

## Lese-Disziplin (Token-Effizienz)

- Dateien größer ~8 KB: erst die Gliederung holen (Document Map / Überschriften), dann gezielt nur den relevanten Abschnitt lesen — kein Voll-Read.
- Backlinks/Frontmatter brauchst du selten. Für reinen Inhalt das billigste Lese-Werkzeug nehmen, nicht das mit dem meisten Overhead.
- Massenoperationen (Größen messen, Links zählen, Muster suchen) laufen lokal über die Dateien, nicht über N Einzelaufrufe eines API-Werkzeugs.

## Namenskonventionen

- `_` Präfix = Meta/Hub-Datei (immer zuerst lesen)
- Decisions-Dateien: `PROJEKTNAME_decisions.md` in `Decisions/`
- Plan-Dateien in `Plans/`: `ProjektName_ErweiterungsName_DokumentTyp.md` — der DokumentTyp (`Plan`, `Spec`, `Anforderungen`, `E2E_Checkliste`, `Konzept`, `Handover`, `NextSteps`, `Vorbehalte`) steht **am Ende**, nie mitten im Namen. Die Liste ist offen: ein treffender neuer DokumentTyp ist erlaubt, entscheidend ist nur die Position. Archiviert → Suffix `_archiviert` + Verschiebung nach `Plans/Archiv/`
- Projektdateien: `PascalCase` oder Repo-Name
- Datum-Format: `YYYY-MM-DD`

## Datum immer messen, nie aus dem Kontext übernehmen

**Vor jedem Schreibvorgang, der ein Datum in den Vault schreibt** (Tagesdatei, Report-`last-run`,
Frontmatter-`created`, Umzugsvermerk), einmal die Systemzeit abfragen. Der Kontext zu
Session-Beginn kann daneben liegen, und der Fehler ist **still**: der Eintrag landet sauber in der
Datei von gestern, nichts schlägt fehl.

Eine einmalige Messung altert außerdem — eine Session kann länger dauern als ein Tag, Abendläufe
besonders. Deshalb wörtlich: **vor jedem** Schreibvorgang, nicht einmal pro Session.

## Wie Dateien angelegt werden

- Neue Projektdatei → `Brain/Projects/PROJEKTNAME.md`
- Neuer Skill → `Brain/Skills/SKILLNAME.md` + Zeile in `_SKILLS.md`
- Neue Decision → `Brain/Decisions/PROJEKTNAME_decisions.md` + Eintrag in `_DECISIONS.md`
- Neues Verzeichnis → immer mit Hub-Datei (`_Name.md`), siehe [[obsidian-hub-anlegen]]
- **Jede neue Datei sofort im zuständigen Hub verlinken** (`_*.md` im selben Verzeichnis, sonst nächsthöherer Hub) — alphabetisch einsortiert, nicht angehängt. **Keine Datei ohne Hub-Eintrag.**
- Wikilinks `[[Dateiname]]` aktiv setzen, um den Graph aufzubauen — **außer in Dailies** (dort nur Dateinamen als Klartext, siehe unten)
- Frontmatter mit `tags` bei neuen Dateien immer setzen

## Wohin gehört was

| Inhalt | Ziel |
|---|---|
| Was heute passiert ist (Hergang, Chronologie) | `Daily/YYYY-MM/YYYY-MM-DD.md` |
| Dauerhaftes Wissen über ein eigenes Projekt | `Projects/` |
| Entscheidung mit Begründung, verworfene Alternative, Vorfall | `Decisions/` |
| Fakten über ein Fremdsystem (kein eigener Code) | `Knowledge/` |
| Wiederkehrender **Ablauf** („wie mache ich X") | `Skills/` — **nicht** `Knowledge/` |
| Einrichtung von Infrastruktur | `Setups/` |
| Plan oder Checkliste vor der Umsetzung | `Plans/` |
| Personen, Ansprache, Kontakt | `People/` |

**Die Daily ist ein chronologisches Log, kein Wissensspeicher.** Wissen wandert in die Zieldatei,
der Hergang bleibt im Tag — und darf beim Kürzen verschwinden.

## Konvention nach `Decisions/` verlagern heißt nicht, sie aus dem Hub zu entfernen

`Decisions/` wird nur auf Nachfrage gelesen, die Hub-Datei dagegen bei jedem Arbeiten. Deshalb:
**Kurzfassung bleibt im Hub** (die Regel selbst, so knapp wie möglich, aber vollständig genug zum
Befolgen), **nach `Decisions/` wandert die Begründung** (warum so, was wurde verworfen, welcher
Vorfall dahintersteckt). Ein Hub-Eintrag, der nur noch `→ [[…_decisions]]` sagt, ist zu dünn.

Prüfstein: *Könnte jemand allein mit dem Hub-Text die Regel korrekt befolgen?* Nein → zu kurz gekürzt.

## Pläne und Checklisten mit Repo-Zwilling

Hat ein Plan eine Datei im `Plan(s)/`-Ordner eines Repos, ist **die Repo-Datei maßgeblich** und die
Notiz unter `Plans/` eine **vollständige Kopie samt aller Prüfpunkte und Häkchen** — nur der Kopf
darf abweichen, der Körper ist zeichengleich (Hash-Gegenprobe). Änderungen fließen nur Repo → Brain,
vor dem Nachlesen `git pull`.

⛔ **Nie Arbeitsteilung** („Häkchen im Repo, im Brain nur eine Zusammenfassung") — auch nicht, wenn
es so im Kopf des Dokuments steht; dann ist der Satz der Fehler und wird entfernt, nicht befolgt.

## Schreiben in den Vault

- **Vor dem Vollschreiben einer Datei unmittelbar davor frisch lesen.** Ein Lesestand vom Sitzungsbeginn ist nach Stunden kein Schreibstand — ein Vollschreiben überschreibt alles Dazwischengekommene ohne Konflikt und ohne Warnung.
- Für reines Hinzufügen anhängen statt ersetzen. Ein Abschnitts-`replace` ist ein Lost-Update-Risiko, wenn seit dem Lesen jemand anders geschrieben hat.
- **Nach jedem ersetzenden Schreiben gegenprüfen**, dass nur die gewollte Änderung drin ist (Gliederung ansehen, `git diff` lesen).
- Beim Umsortieren einer Datei ist dem Diff nicht zu trauen — er zeigt fast jede Zeile als verschoben. Belegen lässt es sich nur mengenmäßig: alle nicht-leeren Zeilen beider Fassungen sortiert vergleichen.
- **Ein vollständiges Neuschreiben erbt stillschweigend jede uncommittete fremde Änderung in der Datei.** Gegenprobe vor dem Stagen: `git diff HEAD -- <datei>`, nicht nur der eigene Vorher/Nachher-Vergleich.
- Texte ohne harte Zeilenumbrüche schreiben: **ein Absatz = eine Zeile**. Auf feste Spaltenbreite umbrochener Fließtext liest sich in Obsidian zerhackt und erzeugt bei jeder Bearbeitung unnötige Diff-Zeilen.
- **Rettbar ist nur, was committet ist.** Nach einem Schreibvorgang zeitnah committen.

Details und echte Zwischenfälle: [[Brain/Skills/Obsidian/obsidian-vault-patch-fallstricke]].

## Was der Agent eigenständig darf

- Dateien lesen, erstellen, patchen
- Wikilinks setzen
- `_INDEX.md` und Tagesdateien aktualisieren
- Neue Projekt-, Skill- und Decision-Dateien anlegen

## Was der Agent NICHT ohne Rückfrage tun soll

- Bestehende Inhalte überschreiben oder löschen
- Dateien verschieben oder umbenennen
- Größere Strukturänderungen
