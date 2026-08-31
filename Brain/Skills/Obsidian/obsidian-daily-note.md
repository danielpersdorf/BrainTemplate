---
tags: [skill, obsidian, daily]
created: 2026-01-01
status: active
---
# Skill: obsidian-daily-note

## Zweck

Wenn der Nutzer sagt *„schreib das in die daily note"*, *„merk dir das für heute"*, *„trag das heute
ein"* oder ähnlich — eine Tagesdatei anlegen oder aktualisieren.

## Pflichtpfad

```
Brain/Daily/YYYY-MM/YYYY-MM-DD.md
```

Die Tagesdatei liegt im **Monatsordner**. Existiert der noch nicht (Monatswechsel), Ordner anlegen,
darin einen Monats-Hub `_YYYY-MM.md` nach der Vorlage in [[Daily/_Daily]] erstellen und diesen oben
in `_Daily.md` verlinken.

**Niemals** woanders ablegen — nicht im Vault-Root, nicht direkt in `Brain/`.

### ⚠️ Das Datum messen, nicht aus dem Session-Kontext übernehmen

Der Kontext, den ein Agent zu Sessionbeginn bekommt, kann daneben liegen. Die Folge fällt nicht auf,
weil nichts fehlschlägt: Der Eintrag landet sauber in der Datei von *gestern*, die
Maintenance-Reports bekommen ein `last-run` von gestern, und der Monats-Hub bekommt keine Zeile,
weil der Tag „schon existiert".

**Eine Messung altert außerdem.** Startet ein Abendlauf um 20:58 und wird die Session am nächsten
Vormittag fortgesetzt, wandert der Wert vom Vorabend in Report, Skill, Tagesdatei und Monats-Hub.
Ein einmal korrekt gemessener Wert ist der gefährlichere Fall, weil er kein Misstrauen mehr weckt.
Deshalb wörtlich: **vor jedem** Schreibvorgang mit Datum, nicht einmal pro Session. Billige
Gegenprobe bei langen Sessions: `git log -1 --date=iso`.

**Ein „Periodic Note"-Pfad-Werkzeug ist keine Absicherung dagegen** — es folgt der
Tageswechsel-Einstellung des Plugins, nicht dem realen Datum, und legt die (falsche) Zieldatei
dabei sogar an. Das Ergebnis sieht völlig plausibel aus. Also: Systemzeit fragen und den Pfad selbst
zusammensetzen.

## Ablauf

### 1. Datei prüfen

- **Existiert** → anhängen bzw. unter der passenden Überschrift ergänzen
- **Existiert nicht** → neu anlegen mit dem Template unten

### 2. Template (nur bei Neuanlage)

```markdown
---
tags: [daily]
created: YYYY-MM-DD
---
---
```

**Kein `# YYYY-MM-DD`-Titel!** Der Dateiname *ist* der Titel — ihn als Überschrift zu wiederholen
ist Redundanz.

**Jede Tagesdatei hat oben eine Trennlinie `---`:** unter dem Frontmatter und den Flag-Zeilen
(`⚠ Maintenance`, `> Gekürzt am …`), über der ersten Überschrift. Reihenfolge also:
Frontmatter → Flags → `---` → Leerzeile → erste `#`-Überschrift. **Keine Leerzeile zwischen
Frontmatter und Flag-Zeile.** Gibt es keine Flags, steht die Trennlinie direkt unter dem Frontmatter.

### 3. Inhalt eintragen

**Überschriften beginnen bei H1:** Thema = `#`, Einzeleintrag darunter = `##`.

- Jeder Eintrag als eigener **`# Heading`** mit Fließtext darunter — **keine** bloße Bullet-Liste ohne Überschriften
- **Ein Thema = genau eine `#`-Überschrift pro Tag — ausnahmslos.** Mehrere Einträge zum selben Projekt an einem Tag → unter der einen `# Projekt`-Überschrift bündeln, je Eintrag eine **`## Thema`**-Unterüberschrift. Nur ein einziger Eintrag → `# Projekt — Thema` genügt.
- **Die Prüfung gilt bei *jedem* Anhängen, nicht nur beim ersten.** Auch der fünfte Eintrag desselben Tages zum selben Projekt wird zur `##` unter der bestehenden `#`. Typischer Fehlerfall: eine lange Session, in der pro Zwischenergebnis eine neue `# Projekt — Detail`-Überschrift entsteht; am Ende stehen sechs `#` zum selben Projekt.
- **Trennlinien `---` gehören zwischen die `#`-Themen, nicht zwischen die `##`.** Eine Trennlinie innerhalb eines Themas zerreißt genau die Gruppierung, die die Struktur herstellt.
- **Vor jedem neuen Eintrag prüfen, ob zum selben Thema schon eine `#`-Überschrift weiter oben steht** — nicht stur chronologisch ans Dateiende anhängen. Existiert sie, dort als `##` ergänzen und ggf. die bisherige Ein-Thema-Überschrift dafür umbauen.
- Vault-Pflegearbeit (Hubs, Konventionen, Graph, Maintenance) läuft unter der festen Überschrift **`# BrainWork`** — exakt so, nicht `# Brain` (das ist der Ordner) und nicht `# BrainWork — Thema`. Das Thema kommt als `##` darunter.
- Knapp und präzise, kein Boilerplate.
- **Keine Wikilinks** — Dateinamen als Klartext in Backticks. Die Tagesdatei soll keine Backlinks auf Zieldateien erzeugen: der Graph zeigt Wissensstruktur, nicht das Protokoll.
- **Keine harten Zeilenumbrüche mitten im Absatz.** Ein Absatz ist eine Zeile; umgebrochen wird nur zwischen Absätzen, Listenpunkten und Tabellenzeilen.

#### 🔑 Gleich so schreiben, wie es nach der Kürzung aussehen soll

**Kurznotiz + Verweis auf die Zieldatei.** Der [[obsidian-daily-redundancy-check]] kürzt nur, was er
als Hergang erkennt — an einem knapp geschriebenen Abschnitt hat er nichts mehr zu holen. Der Hebel
liegt im Schreiben, nicht im Kürzen: eine Tagesdatei, die von vornherein „Kurznotiz + Verweis" ist,
kommt gar nicht erst auf 15 KB. Prüfstein beim Schreiben derselbe wie beim Kürzen: steht am Ende des
Absatzes ein `→ Zieldatei`, ist der Text davor Hergang und kann sofort weg.

#### Beispiel

```markdown
# WebApp

## Login-Bug im Safari
Ursache war die SameSite-Einstellung des Session-Cookies. Fix in 1.4.2 → Details in `Projects/WebApp`.

## Deployment auf Staging
Läuft, Smoke-Test grün.

---

# BrainWork

## Hub-Sortierung repariert
…
```

Falsch wäre dieselbe Datei als vier `#`-Überschriften mit Trennlinien dazwischen.

### 4. Monats-Hub aktualisieren

Nach dem Schreiben den **Monats-Hub** `Brain/Daily/YYYY-MM/_YYYY-MM.md` aktualisieren
(nicht `_Daily.md` — dort stehen nur die Monats-Hubs). Neue Zeile **oben** in der Index-Tabelle
(absteigend chronologisch):

```markdown
| [[YYYY-MM-DD]] | Kurze Highlights |
```

Bei einem bestehenden Tag den Highlight-Text ergänzen — **aber neu geschrieben, nicht angehängt**.

⚠️ **Die Highlight-Zeile ist ein Stichwortverweis, keine Zusammenfassung und erst recht keine
Kopie.** Sie sagt, **worum es an dem Tag ging** — Projekt plus Stichwort, Themen mit `;` getrennt.
Details stehen in der Tagesdatei und in den Zieldateien. Der Hub wird bei *jedem* Daily-Zugriff
mitgelesen.

🛑 **Obergrenze 250 Zeichen je Zeile, Zielkorridor 120–200.** Darüber wird die Zeile umgeschrieben,
nicht ergänzt.

Zwei Regeln, ohne die die Zahl nichts nützt:

- **Beim Ergänzen wird die ganze Zeile neu formuliert, nicht hinten etwas drangehängt.** Genau daran wächst sie: über einen langen Arbeitstag kommen fünf Ergänzungen, jede für sich vertretbar, und am Abend steht dort ein Aufsatz.
- **Nach dem Schreiben die Zeichenzahl messen, nicht schätzen** — geschätzt wird sie regelmäßig zu kurz:
  ```powershell
  (Get-Content _YYYY-MM.md -Encoding UTF8 | Select-String -SimpleMatch '[[YYYY-MM-DD]]').Line.Length
  ```
  ⚠️ **Nicht mit `-like '*[[YYYY-MM-DD]]*'` messen:** `[` öffnet in PowerShell-Wildcards eine
  Zeichenklasse, der Aufruf scheitert je Zeile mit `WildcardPatternException` und liefert am Ende
  eine `0`. Wer die 0 für die Zeilenlänge hält, hält jede Zeile für konform. Deshalb `-SimpleMatch`.
  - Vorfilter über alle Zeilen (Git Bash, im Monatsordner):
    ```bash
    awk '{ if (length($0) > 250 && /^\| \[\[/) print length($0)": "substr($0,1,40) }' _YYYY-MM.md
    ```
    Leere Ausgabe heißt konform. `awk` zählt **Bytes**, nicht Zeichen — jeder Umlaut und jedes `→`
    zählt doppelt. Als Vorfilter taugt das (es meldet eher zu viel als zu wenig), die maßgebliche
    Zahl liefert der PowerShell-Aufruf.

**Warum so scharf:** Ein Monats-Hub, der zu wachsen anfängt, ist schwer zu stoppen — Kürzen im
Nachhinein hält typischerweise eine Woche, dann fressen die neu geschriebenen Zeilen die Ersparnis
wieder auf. Das Problem entsteht und endet beim Schreiben.

⚠️ **Eine am Ist-Zustand kalibrierte Grenze ist keine Regel, sondern eine Beschreibung des
Problems.** Wer den Richtwert aus dem bereits aufgeblähten Bestand ableitet, bekommt eine Zahl, die
den Wildwuchs bestätigt. Die 250 oben sind an *funktionierenden* Monaten gemessen.

## Trigger-Phrasen

- „schreib das in die daily note" · „mach dir notes in der daily" · „merk dir das für heute"
- „trag das heute ein" · „daily note: …" · „notier das für heute"

## Was NICHT in die Tagesdatei gehört

| Inhalt | Ziel |
|---|---|
| Dauerhaftes Projekt-Wissen | `Brain/Projects/` |
| Entscheidungen mit Begründung | `Brain/Decisions/` |
| Abläufe | `Brain/Skills/` |
| Personen | `Brain/People/` |

Die Tagesdatei ist ein **chronologisches Log**, kein Wissensspeicher.
