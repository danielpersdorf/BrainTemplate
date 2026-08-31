---
tags: [skill, obsidian, maintenance, daily]
created: 2026-01-01
status: active
---
# Skill: obsidian-daily-redundancy-check

Prüft pro Abschnitt einer Tagesdatei, ob das darin enthaltene **Wissen** bereits an der richtigen
Stelle im Brain liegt (Projekt-, Skill-, Decision-, Setup-, People-Datei). Ziel-Zustand: die
Tagesdatei enthält nur Überschrift + Kurznotiz + Verweis, die Details stehen in der Wissensbasis.
Erst wenn das verifiziert ist, darf gekürzt werden.

**Pipeline: Analyse (Report) → Extraktion (automatisch) → Re-Analyse (Verifikation) → Kürzung.**

Die Extraktion darf automatisch laufen, weil sie **rein additiv** ist: sie ergänzt Wissen in
Zieldateien, entfernt oder ändert aber nie etwas — schlimmstenfalls entsteht mehr Redundanz, die die
Kürzung später auflöst.

Die Kürzung darf automatisch laufen, wenn die **Re-Analyse `ok` meldet**. Das grüne
Re-Analyse-Ergebnis ist das Sicherheits-Gate. Sicherheitsnetz darunter: die ungekürzte Fassung
bleibt über die täglichen Commits in der Git-Historie, und der „Gekürzt am"-Vermerk im Dateikopf
zeigt an, dass es eine längere Fassung gab.

> ⚙️ Als Routine wird der Skill mit dem nackten Aufruf „lies den Skill und führe ihn aus" gestartet —
> der Task-Prompt trägt bewusst **keine** Ablauf-Details, damit Skill und Prompt nicht auseinanderlaufen.

---

## Scope

**Routine-Modus (ohne genanntes Datum):** Wasserzeichen `geprüfter-tag` aus dem Report lesen, **alle
noch ungeprüften Tage bis einschließlich Vortag** abarbeiten, **ältester zuerst**, max. 3 je Lauf.

- **Nie „einfach der Vortag".** An einem Montag ist der Vortag ein Sonntag ohne Tagesdatei — der Freitag fiele damit dauerhaft durch. Ein Wasserzeichen kann keinen Tag überspringen, eine Vortags-Regel schon.
- **Ältester zuerst**, damit das Wasserzeichen lückenlos vorrückt. Nähme man den jüngsten ungeprüften Tag, blieben ältere Lücken für immer offen.
- **Obergrenze 3 Tage je Lauf.** Bleibt danach etwas offen, im Report unter „offen" namentlich nennen — die Routine holt es an den Folgetagen nach.
- Der **laufende Tag wird nie geprüft** (er kann sich noch ändern), der Vortag ist garantiert abgeschlossen.

**Manuell:** ein genanntes Datum oder ein Bereich (Backfill des Bestands).

**Nicht geprüft:** die Monats-Hubs — deren Highlight-Zeilen sind bewusst redundante Kurzfassungen.

---

## Wissen vs. Hergang

| Kategorie | Beispiel | Gehört |
|---|---|---|
| **Wissen** — dauerhaft gültige Fakten | Fix + Ursache, Konfiguration, Diagnose-Signatur, Entscheidung + Begründung | nach `Projects/`, `Skills/`, `Decisions/`, `Setups/`, `People/` |
| **Hergang** — chronologischer Kontext | wer sagte was wann, Zwischenschritte, Fehldiagnosen, Reihenfolge der Erkenntnisse | bleibt in der Tagesdatei — und **fällt bei der Kürzung weg**, sobald das Wissen extrahiert ist |
| **Nächster Schritt** | offene Tasks | **muss** in der Projektdatei bzw. Hub-Spalte stehen — beim Extrahieren synchronisieren |

🔑 **Wo der Verweis steht, ist der Absatz davor Hergang und geht.** Das ist die Regel, an der sich
entscheidet, ob eine Kürzung wirkt: ein ausformulierter Absatz, der am Ende ein `→ Zieldatei`
mitführt, ist nicht gekürzt worden — der Verweis beweist, dass der Text davor entbehrlich ist.

## Coverage-Stufen (pro Abschnitt)

- `covered` — jeder Wissens-Fakt ist in der Zieldatei vorhanden → Abschnitt kürzbar
- `partial` — Teile fehlen; der Report nennt **was** fehlt und **wohin** es gehört
- `missing` — nichts abgelegt; der Report schlägt eine Zieldatei vor (ggf. neu → [[obsidian-hub-anlegen]])

### Die Stufe gilt pro Abschnitt — die Gegenprobe muss pro **Wert** laufen

> ⚠️ **Ein Abschnitt kann `covered` sein und trotzdem Werte enthalten, die es nirgends sonst gibt.**
> Die Stufe prüft das *Thema*, nicht die einzelnen Bezeichner. Bezeichner, Fehlercodes, Dateinamen,
> Commit-Hashes und SQL-Fragmente stehen typischerweise nur an einer Stelle — und eine konsequente
> Kürzung vernichtet sie.

Deshalb **vor jeder Kürzung** über alle Code-Spans des Tages gegenprüfen:

```bash
grep -o '`[^`]\+`' Daily/YYYY-MM/YYYY-MM-DD.md | sed 's/^`//;s/`$//' | sort -u | while IFS= read -r s; do
  [ ${#s} -lt 3 ] && continue
  grep -rqF -- "$s" --include='*.md' Projects Plans Skills Decisions Knowledge People Setups || echo "NUR IN DAILY: $s"
done
```

Vier Fallstricke des Vergleichs:

- Er ist **exakt** — Schreibvarianten melden falsch positiv (`Stat = '2'` gegen `Stat='2'`). Vor dem Extrahieren einzeln nachschlagen.
- Verschachtelte Backticks erzeugen Müllzeilen, die man ignoriert.
- **Pfadangaben** schlagen fast immer an, weil der Pfad als Zeichenkette in keiner anderen Datei steht. Das sind die `→ Zieldatei`-Verweise selbst, kein Wissen — ignorieren.
- **Commits des Vault-Repos selbst sind kein Wissen** — die Tagesdatei liegt im selben Repo, `git log` findet sie jederzeit. Fremd-Repo-Hashes sind das Gegenteil: von hier nicht rekonstruierbar, also extrahieren. Faustregel: Beschreibt der Commit eine Änderung **an einer Brain-Datei**, darf er weg.

**Ein Treffer blockiert die Kürzung nicht — er löst eine Extraktion aus.** Ein Wert, der nur in der
Tagesdatei steht, ist ein Fehler in der Extraktion, kein Grund, den Absatz zu behalten. Also:
extrahieren, kürzen, dieselbe Gegenprobe **vault-weit inklusive `Daily/`** wiederholen — sie muss
leer ausgehen.

### „Steht im Repo" ist nicht `covered`

Eine Repo-README, eine `CLAUDE.md` im Projekt oder ein Kopfkommentar im Code zählen **nicht** als
Ablage. Das Brain kann sie nicht gegenlesen, sie fehlen auf jedem Rechner ohne diesen Klon, und beim
Kunden hat sie niemand. Wo eine Repo-Datei die Langfassung trägt, gehört die belastbare Kurzfassung
trotzdem ins Brain — die Repo-Datei ist die *ausführlichere*, nie die *einzige* Fassung. (Dieselbe
Erwägung wie *Kurzfassung bleibt im Hub* → [[obsidian-decision-scan]].)

---

## Ablauf

### Schritt 0 — Datum messen

Systemzeit abfragen, vor dem ersten Lesen. Gilt für `last-run`, den Report-Titel, den
„Gekürzt am"-Vermerk und jedes `created` neu angelegter Dateien.

### Schritt 1 — Zieltag(e) bestimmen

```
→ geprüfter-tag aus Maintenance/daily-redundancy-report.md lesen (= Wasserzeichen)
→ Daily/YYYY-MM/*.md sammeln (rekursiv über die Monatsordner!)
  mit Wasserzeichen < Datum <= Vortag, aufsteigend sortiert
→ auf die ersten 3 begrenzen; der Rest kommt in den Report unter „offen"
→ keine Datei in der Liste → Report „nichts zu prüfen", status: ok,
  Wasserzeichen UNVERÄNDERT lassen (nicht auf den Vortag vorziehen!)
```

Das Wasserzeichen nur auf einen Tag setzen, der **tatsächlich geprüft** wurde — sonst überspringt
der nächste Lauf die Lücke. Fehlt für einen Tag im Bereich die Tagesdatei (Wochenende, Urlaub),
zählt er als geprüft und blockiert nichts.

Mehrere Zieltage werden **einzeln** durch Schritt 2–6 gefahren; der Report fasst sie am Ende zusammen.

### Schritt 2 — Abschnitte analysieren

```
Pro # Abschnitt:
  → Projekt/Thema aus der Überschrift bestimmen
  → Wissens-Fakten extrahieren (Hergang aussortieren)
  → Zieldatei(en) ermitteln: über die Hubs; bei Unklarheit Volltextsuche
  → Zieldatei(en) lesen und Fakt für Fakt abgleichen (semantisch, nicht wörtlich)
  → Stufe vergeben: covered | partial | missing
```

### Schritt 3 — Report schreiben

`Brain/Maintenance/daily-redundancy-report.md` **komplett überschreiben**:

```markdown
---
last-run: YYYY-MM-DD
geprüfter-tag: YYYY-MM-DD
status: ok | action-required
tags: [maintenance]
created: YYYY-MM-DD
---

# Daily Redundancy Report — geprüft: YYYY-MM-DD

Abschnitte geprüft: N
covered: N | partial: N | missing: N

## covered (kürzbar)
- „WebApp — Login-Bug" → Projects/WebApp ✓

## partial
- „WebApp — Deploy" → Projects/WebApp
  fehlt: Diagnose-Signatur „Health-Check antwortet 200 mit leerem Body"

## missing
- „Neue Schnittstelle" → Vorschlag: Projects/PartnerAPI
```

**`status`-Regel:** `ok` wenn alle Abschnitte `covered` (oder nichts zu prüfen) **und** keine
Konflikte gemeldet, sonst `action-required`.

**`geprüfter-tag` ist das Wasserzeichen für den nächsten Lauf** — bei mehreren Tagen in einem Lauf
den **jüngsten tatsächlich geprüften** Tag eintragen. Alle geprüften Tage zusätzlich im Body nennen,
offen gebliebene unter „offen".

**Frontmatter-Regeln:** `tags: [maintenance]` als echtes YAML-Array; `created` aus dem alten Report
übernehmen; komplettes Frontmatter im Schreib-Payload — nichts nachträglich patchen.

### Schritt 4 — Extraktion (automatisch, rein additiv)

Für `partial`/`missing`-Funde:

```
→ Fehlende Fakten in die Zieldatei einarbeiten (bestehenden Stil übernehmen)
→ NUR ERGÄNZEN — bestehenden Inhalt der Zieldatei niemals ändern oder löschen.
  Widerspricht ein Fakt dem Bestand (z.B. veralteter „Nächster Schritt"):
  nicht überschreiben, sondern im Report als Konflikt melden
→ Zieldatei darf KEIN Repo-Spiegel sein (Plan/Checkliste mit Zwilling im Plan(s)/-Ordner):
  der Körper ist dort zeichengleich, eine Ergänzung im Brain wird beim nächsten
  Nachziehen ersatzlos überschrieben. Datei ohne Zwilling wählen oder über das Repo gehen
→ Neue Dateien nach CLAUDE.md-Regeln: Frontmatter, Hub-Eintrag alphabetisch
→ „Nächster Schritt" in Projektdatei/Hub-Spalte ergänzen bzw. bei Konflikt melden
→ Gegenlesen: Fakt jetzt vorhanden, Rest der Zieldatei unverändert?
```

### Schritt 5 — Re-Analyse (Verifikation)

Schritt 2 für denselben Tag wiederholen. Jetzt müssen **alle** Abschnitte `covered` melden — erst
dann ist die Kürzung freigegeben. Report aktualisieren.

### Schritt 6 — Kürzung

Bedingung für die automatische Kürzung: eine **frische** Re-Analyse meldet `ok`. Nicht auf einen
alten Report verlassen — zwischen Report und Kürzung kann sich der Vault geändert haben.

```
Zielform pro Abschnitt:
  # Überschrift
  1–2 Sätze Kurznotiz + Verweis: → Details in `Zieldatei`

  ⚠ Das ist die Zielform, kein Richtwert nach oben. Steht am Ende ein
    ausformulierter Absatz von 800 Zeichen mit einem → am Schluss, ist
    NICHT gekürzt worden.

→ KEIN Datums-Titel. Nach dem Frontmatter kommt OHNE Leerzeile der
  „> Gekürzt am …"-Hinweis, dann eine Trennlinie ---, dann die # Abschnitte
→ Ältere Tagesdateien mit bloßer Bullet-Liste dabei in # Abschnitte umwandeln
→ Mehrere Abschnitte zum selben Projekt unter EINE # Überschrift bündeln,
  je Thema eine ## Unterüberschrift
→ KEINE Wikilinks — Dateinamen als Klartext in Backticks
→ Dateikopf: „> Gekürzt am YYYY-MM-DD per obsidian-daily-redundancy-check"
→ Stale gewordene Verweise AUF die Tagesdatei in den Zieldateien durch einen
  Datumsvermerk ersetzen
```

Wiederherstellung im Zweifel: `git log -- Brain/Daily/YYYY-MM/YYYY-MM-DD.md`.

---

## Regeln

- **Niemals kürzen ohne frische grüne Re-Analyse** — `partial`, `missing` oder gemeldete Konflikte blockieren die Kürzung
- **Extraktion ist rein additiv** — sie ergänzt nur; Konflikte werden gemeldet, nicht aufgelöst
- **Wasserzeichen nie auf einen unbearbeiteten Tag vorziehen** — `geprüfter-tag` darf nur wachsen, wenn der Tag wirklich durch die Pipeline lief oder gar keine Tagesdatei hat
- Hergang wird nicht extrahiert — er ist Kontext, kein Wissen
- **Chronologie wahren:** beim Kürzen den Stand des Tages beibehalten. Einen an einem späteren Tag erledigten Punkt **nicht** so umformulieren, als sei er an diesem Tag erledigt worden; ein am Tag offenes TODO bleibt ein TODO des Tages.
  - **Die eine erlaubte Ausnahme: ein datierter Klammerzusatz**, wenn die Aussage des Tages den nächsten Leser sonst in die Irre schickt — eine Messung, die sich später als umgebungsspezifisch herausgestellt hat, bekommt einen Zusatz wie „(Ursache erst am … gefunden → `Zieldatei`)". Der Satz des Tages bleibt **unverändert stehen**, der Zusatz trägt sein eigenes Datum. Erlaubt ist nur *hinzufügen und datieren*, nie den Stand nachziehen.
- Semantisch abgleichen, nicht wörtlich — der Fakt zählt, nicht die Formulierung
- Bei Extraktion: alphabetische Hub-Konvention und Hub-Verlink-Pflicht einhalten
- Die Highlight-Zeile im Monats-Hub bleibt bei der Kürzung unverändert
- Bei Unsicherheit, ob Wissen oder Hergang: als `partial` melden und den Nutzer entscheiden lassen

---

## Aufruf

> „Führe obsidian-daily-redundancy-check aus"
> „Redundancy check für [Datum]"
> „Ist das Wissen aus der Daily schon im Brain abgelegt?"
> „Kürz die Daily vom [Datum]" (→ komplette Pipeline inkl. Extraktion)
