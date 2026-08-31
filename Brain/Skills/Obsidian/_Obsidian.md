---
tags: [skill, obsidian, hub]
created: 2026-01-01
---
# Obsidian

> Skills rund um den Vault selbst — Tageseinträge, Wartung, Struktur-Reparaturen, Git-Routine.

> 🔤 Konvention: Listen-Einträge in dieser Datei immer alphabetisch sortieren — neue Einträge an der korrekten Position einfügen, nicht unten anhängen.

## Werkzeug-Fallen beim Schreiben in den Vault

Gilt für alle Skills hier, deshalb einmal zentral. Die Stolperfallen der Schreib-Werkzeuge selbst
stehen ausführlich in [[obsidian-vault-patch-fallstricke]] — **vor einer Serie von
Vault-Schreibvorgängen mitlesen**.

- 🛑 **Ein abschnittsersetzendes Schreiben ist ein Lost-Update-Risiko.** `replace` überschreibt den ganzen Abschnitt mit dem, was du selbst im Kopf hast; alles, was jemand anders inzwischen darin ergänzt hat, verschwindet stillschweigend — ohne Konflikt, ohne Warnung, weil git nur die Datei als Ganzes sieht und der Abschnitt syntaktisch heil bleibt. **Vor jedem Ersetzen `git pull` und den Zielabschnitt frisch lesen**, danach die Gliederung gegenprüfen (ein Patch kann auch die Überschriftenzeile anschneiden). Bei Verdacht: `git diff <letzter fremder Commit> -- <datei>` auf entfernte Zeilen ansehen, wiederherstellen mit `git checkout <commit> -- <datei>` und die eigenen Ergänzungen danach einspleißen.
- ⚠️ **Anhängen landet am Ende des Abschnitts — auch mitten in einer sortierten Tabelle.** Eine neue Tabellenzeile steht damit hinter der letzten Zeile, nicht an ihrer alphabetischen Position. Genau das verlangen aber alle Hubs. **Vor dem Anhängen prüfen, ob das Ziel eine sortierte Liste ist**; wenn ja, gezielt einfügen statt anhängen.
- 🔧 **Für punktuelle Änderungen ist der Dateizugriff billiger als ein API-Werkzeug.** Ein Abschnitts-Patch kann nur ganze Abschnitte ersetzen — für zwei Wikilinks in einer 40-KB-Datei oder eine einzelne Tabellenzeile ist das absurd teuer und fehleranfällig; jedes Neuschreiben ist eine Gelegenheit, Text zu verlieren. Lesen–Ersetzen–Schreiben erledigt es punktgenau.
- 🔴 **Encoding auf beiden Seiten angeben, nicht nur beim Schreiben.** `Get-Content -Raw` ohne `-Encoding UTF8` liest eine UTF-8-Datei als ANSI und schreibt beim Zurückschreiben den **kompletten Altbestand** als Mojibake. Der neu eingefügte Text ist dabei korrekt, alles andere kaputt — deshalb fällt es beim Drüberschauen der eigenen Änderung nicht auf.
  ```powershell
  $c = Get-Content $p -Raw -Encoding UTF8            # <- -Encoding UTF8 ist Pflicht
  $c = $c.Replace($alt, $neu)
  [System.IO.File]::WriteAllText($p, $c, (New-Object System.Text.UTF8Encoding($false)))
  ```
  `Set-Content -Encoding utf8` tut es auch, schreibt in PowerShell 5.1 aber ein BOM — für Markdown unschön, für Commit-Nachrichten schädlich. **Nach jedem Direktzugriff vaultweit auf `â€`, `Ã`, `Â` prüfen**, nicht nur die geänderte Stelle ansehen.
- 🔴 **`-like "*$text*"` taugt nicht zum Prüfen von Ankertexten mit Backticks.** Im Wildcard-Muster ist der Backtick das Escape-Zeichen — ein Anker mit Inline-Code wird nicht gefunden, obwohl er wörtlich in der Datei steht. Der Abbruch sieht dann wie „Text fehlt" aus und verleitet dazu, den Anker zu ändern statt das Werkzeug. **`.Contains()` oder `IndexOf()` verwenden**, und für einen Bereich zwischen zwei Ankern mit `Substring` spleißen statt mit Regex.
- 🔴 **Commit-Nachricht in eine Datei schreiben und `git commit -F` nehmen, nicht `-m` mit Here-String.** Eine PowerShell-Here-String mit Anführungszeichen im Text zerfällt beim Übergeben an das native `git` in einzelne Argumente — git meldet `pathspec '…' did not match any file(s)` und der Commit bleibt aus. Die Nachrichtendatei **ohne BOM** schreiben, sonst steht das BOM im Commit-Betreff und ist nur per `--amend` loszuwerden.
- 🔴 **`git add -A` ist im Vault fast nie richtig.** Der Vault ist ein geteilter Arbeitsbereich, in dem meist etwas Fremdes offen liegt — ein pauschales Stagen zieht fremde Arbeit in den eigenen Commit. Vor einem Zwischen-Commit `git status` lesen und **nur die eigenen Zieldateien einzeln stagen**. Die Ausnahme ist der abendliche Sammel-Commit in [[obsidian-daily-commit-push]] — der soll bewusst alles mitnehmen.
- 🔴 **Ein vollständiges Neuschreiben erbt stillschweigend jede uncommittete fremde Änderung in der Datei.** Wer eine Datei umsortiert und danach nur den eigenen Diff prüft, sieht das nicht: der fremde Arbeitsstand steht schon drin, wandert in denselben Commit und ist im eigenen Diff unauffällig. **Gegenprobe vor dem Stagen: `git diff HEAD -- <datei>`.**
- 🔴 **Ein negativer Befund ist zuerst ein Verdacht gegen die Messung.** Meldet eine selbstgebaute Prüfung „nichts gefunden", ist sie erst dann ein Beleg, wenn sie gegen einen bekannt abweichenden Fall angeschlagen hat. Ein neu gebauter Prüfschritt, der beim ersten Lauf schweigt, hat nichts bewiesen.
- ⚠️ **Wenn Obsidian die Datei gerade offen hat, gewinnt der zuletzt schreibende** — nach einem Direktzugriff also nicht im selben Atemzug noch über die API in dieselbe Datei schreiben.
- ⚠️ **Parallel laufende Routinen verschieben Zählwerte.** Wächst die Dateizahl zwischen zwei Durchgängen, ist das meist keine Fremdänderung, sondern ein gleichzeitig laufender Wartungslauf. **Maßgeblich ist immer der letzte Durchgang.**

## Skills

| Skill | Wann verwenden |
|---|---|
| [[obsidian-alphabetic-index-repair]] | Prüft und repariert die alphabetische Sortierung der Listen in allen Hub-Dateien (`_*.md`) |
| [[obsidian-brain-update]] | Sessions des Tages auswerten und die relevanten Inhalte in die Tagesdatei schreiben |
| [[obsidian-broken-links-report]] | Findet tote Wikilinks — Report, ändert nichts |
| [[obsidian-daily-commit-push]] | Täglicher Git-Commit des Vaults inkl. Maintenance-Flag in der Tagesdatei |
| [[obsidian-daily-note]] | „schreib das in die daily" → Tageseintrag anlegen/ergänzen, Struktur und Monats-Hub |
| [[obsidian-daily-redundancy-check]] | Prüft, ob das Wissen einer Tagesdatei im Brain abgelegt ist — extrahiert, dann kürzt |
| [[obsidian-daily-todos-verschieben]] | Offene TODOs der jüngsten Tagesdatei auf den nächsten Arbeitstag übertragen |
| [[obsidian-decision-scan]] | Findet verbindliche Regeln außerhalb von `Decisions/` — Report, Umzug nur nach Bestätigung |
| [[obsidian-frontmatter-repair]] | Ergänzt fehlendes Frontmatter (`tags`, `created`) — nie überschreibend |
| [[obsidian-hub-anlegen]] | Neue Hub-Datei/neues Verzeichnis nach Template anlegen |
| [[obsidian-orphan-check]] | Findet Waisen (0 Backlinks) und Dateien ohne Hub-Eintrag — Reparatur nur nach Bestätigung |
| [[obsidian-project-split]] | Teilt eine zu große Projektdatei in Hub + Teildateien — 1:1, nur nach Bestätigung |
| [[obsidian-token-efficiency-analysis]] | Vermisst die Lese-Pfade, meldet Archiv-/Split-Kandidaten — Report-only |
| [[obsidian-vault-patch-fallstricke]] | Stolperfallen beim Schreiben in den Vault — vor einer Schreibserie lesen |
