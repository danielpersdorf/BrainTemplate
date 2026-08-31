---
tags: [skill, obsidian, daily]
created: 2026-01-01
status: active
---
# Skill: obsidian-brain-update

Wertet die Sessions des Tages aus und schreibt die dokumentationswürdigen Ergebnisse in die
Tagesdatei. Gedacht als abendliche Routine, funktioniert auch auf Zuruf („update den Brain mit heute").

Manuelles Gegenstück: [[obsidian-daily-note]] (Einzeleintrag auf Ansage).

---

## Pipeline

```
[1] Vorfilter     → nur Datum, alle heutigen Sessions      (kostenlos)
       ↓
[2] Extrakt       → komprimieren, Tool-Results weg         (läuft lokal, kein LLM)
       ↓
[3] Bewerten      → Relevanz entscheiden                   (wenige Token)
       ↓
[4] Tagesdatei    → Daily/YYYY-MM/YYYY-MM-DD.md
```

> Die Komprimierung in Schritt 2 ist die eigentliche Einsparung (80–90 % Volumenreduktion). Nach
> ihr produzieren kleine Sessions kaum noch Token, und die Relevanzentscheidung kann am
> vollständigen Tagesbild getroffen werden statt an einer Stichprobe.

---

## Schritt 0 — Datum messen

Systemzeit abfragen. **Nicht aus dem Session-Kontext übernehmen** — sonst landet der ganze Lauf in
der Tagesdatei von gestern, ohne dass irgendetwas fehlschlägt. → [[obsidian-daily-note]]

## Schritt 1 — Vorfilter

Einziges Kriterium: die Session-Datei wurde heute geschrieben.

```powershell
$today = (Get-Date).Date
$root  = "$env:USERPROFILE\.claude\projects"

$candidates = Get-ChildItem $root -Recurse -Filter "*.jsonl" |
  Where-Object { $_.LastWriteTime -ge $today } |
  Sort-Object LastWriteTime
```

*(Pfad an den jeweiligen Agenten anpassen — jedes Werkzeug legt seine Transkripte woanders ab.)*

## Schritt 2 — Extrakt

**Tool-Results werden vollständig ignoriert** — das spart den Großteil des Volumens.

### Primäre Signale

| Signal | Quelle |
|---|---|
| Git Commits | Shell-Aufrufe mit `git commit` |
| Git Push | Shell-Aufrufe mit `git push` |
| Vault-Schreibvorgänge | Anzahl der Schreiboperationen in den Vault |
| Bearbeitete Dateien | `Edit`/`Write` → nur der **Pfad**, kein Inhalt |

### Sekundäre Signale

| Signal | Filter |
|---|---|
| Test-Läufe (`dotnet test`, `pytest`, `jest`, `cargo test`, `npm test`) | dedupliziert, max. 3 |
| Tool-Nutzung (Namen + Anzahl) | zeigt das Aktivitätsprofil |
| Assistant-Texte | letzte 25 Blöcke, max. 600 Zeichen |

> Reine Build-Befehle werden nicht erfasst — zu rauschig.

```powershell
$extracts = foreach ($file in $candidates) {
  $assistantTexts = [System.Collections.Generic.List[string]]::new()
  $toolCalls      = @{}
  $gitCommits     = [System.Collections.Generic.List[string]]::new()
  $editedFiles    = [System.Collections.Generic.List[string]]::new()
  $testCmds       = [System.Collections.Generic.List[string]]::new()
  $vaultWrites    = 0

  foreach ($raw in [System.IO.File]::ReadLines($file.FullName)) {
    try {
      $j = $raw | ConvertFrom-Json
      $role    = if ($j.role)              { $j.role }    else { $j.message.role }
      $content = if ($null -ne $j.content) { $j.content } else { $j.message.content }
      if ($role -ne "assistant" -or -not $content) { continue }

      foreach ($block in $content) {
        if ($block.type -eq "text" -and $block.text.Length -gt 10) {
          $assistantTexts.Add(($block.text.Substring(0, [Math]::Min(600, $block.text.Length)) -replace '\n+', ' '))
        }
        if ($block.type -ne "tool_use") { continue }

        $n = $block.name; $cmd = $block.input.command
        $toolCalls[$n] = ($toolCalls[$n] -as [int]) + 1

        if ($n -match 'Bash|PowerShell' -and $cmd) {
          if ($cmd -match 'git commit') { $gitCommits.Add(($cmd -replace '\s+', ' ')) }
          if ($cmd -match 'dotnet test|jest|pytest|cargo test|npm run test') {
            $norm = ($cmd -replace '\s+', ' ').Trim()
            if ($testCmds -notcontains $norm) { $testCmds.Add($norm) }
          }
        }
        if ($n -match 'vault_write|vault_patch|vault_append') { $vaultWrites++ }
        if ($n -in @("Edit","Write","NotebookEdit")) {
          $p = if ($block.input.file_path) { $block.input.file_path } else { $block.input.path }
          if ($p) { $editedFiles.Add($p) }
        }
      }
    } catch { <# ungültige Zeilen überspringen #> }
  }

  [PSCustomObject]@{
    Project     = $file.Directory.Name
    SizeKB      = [math]::Round($file.Length / 1KB, 0)
    Tools       = ($toolCalls.GetEnumerator() | ForEach-Object { "$($_.Key)($($_.Value))" }) -join ", "
    GitCommits  = $gitCommits -join " | "
    VaultWrites = $vaultWrites
    TestCmds    = ($testCmds | Select-Object -Last 3) -join " | "
    EditedFiles = ($editedFiles | Select-Object -Unique) -join ", "
    LastTexts   = ($assistantTexts | Select-Object -Last 25) -join " /// "
  }
}
$extracts | ConvertTo-Json -Depth 2
```

## Schritt 3 — Bewerten

**Dokumentationswürdig, wenn mindestens eines zutrifft:**

| Kriterium | Bedeutung |
|---|---|
| `GitCommits` befüllt | Code-Arbeit abgeschlossen und committet |
| `EditedFiles` + `TestCmds` vorhanden | aktive Entwicklung auch ohne Commit |
| `VaultWrites` > 0 | etwas wurde im Brain dokumentiert oder analysiert |
| `LastTexts` umfangreich und inhaltlich substanziell | konzeptionelle Session — Entscheidung, Architektur, Planung |

> **Nicht jede Session braucht Git-Aktivität, um dokumentationswürdig zu sein.** Eine Session mit
> vielen Assistant-Texten aber ohne Commits kann eine Analyse oder eine Architekturentscheidung
> sein — das gehört genauso ins Brain.

**Ignorieren:** reine Lesezugriffe und kurze Fragen · rein organisatorisches (das Brain-Update
selbst, Einzeiler) · sehr kleine Sessions **ohne** eines der obigen Signale.

## Schritt 4 — Tagesdatei schreiben

Zielpfad, Struktur und Monats-Hub exakt nach [[obsidian-daily-note]]. Kurz:

- Existiert die Datei → neuen `#`-Abschnitt anhängen; **vorher prüfen, ob zum Thema schon eine `#`-Überschrift existiert** → dann als `##` darunter
- Existiert sie nicht → mit Frontmatter-Template anlegen, kein Datums-Titel
- **Ein Projekt = eine `#`-Überschrift pro Tag**
- Danach den **Monats-Hub** um eine Zeile oben ergänzen (250 Zeichen Obergrenze, ganze Zeile neu formulieren statt anhängen)
- **Gleich in der Zielform schreiben:** Kurznotiz + Verweis auf die Zieldatei, nicht der ganze Hergang

**Duplikat-Schutz:** Commit-Hash oder identischer Eintrag bereits vorhanden → Session überspringen.

---

## Regeln

- Datum vor jedem Schreibvorgang messen
- Tool-Results nie ins Extrakt aufnehmen — sie sind das Volumen, nicht die Information
- Aus `EditedFiles` nur Pfade, nie Inhalte
- Die Tagesdatei ist ein Log: 1–4 Sätze je Session, Details gehören in die Zieldateien

## Aufruf

> „Update den Brain mit heute"
> „Werte die heutigen Sessions aus"
