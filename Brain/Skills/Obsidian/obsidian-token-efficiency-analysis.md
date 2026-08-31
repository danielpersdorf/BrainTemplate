---
tags: [skill, obsidian, maintenance, token-effizienz]
created: 2026-01-01
status: active
---
# Skill: obsidian-token-efficiency-analysis

Vermisst das Brain auf die Token-Effizienz seiner **Lese-Pfade** und schreibt einen Report.
**Ändert nichts** — Report-only; umgesetzt wird auf Zuruf durch andere Skills.

Grundgedanke: Nicht die Gesamtgröße des Vaults kostet Tokens, sondern das, was auf **heißen
Lese-Pfaden** liegt (Navigationskette, Projektdateien, Tagesdateien). Große Dateien auf kalten
Pfaden sind unkritisch — **groß ≠ ineffizient, wenn selten gelesen**.

Rhythmus: wöchentlich genügt, die Größenverhältnisse ändern sich langsam.

---

## Ausschlüsse (Vollständigkeits-Regeln)

Diese Bereiche werden **vermessen, aber nie für Kürzung, Deduplizierung oder Split empfohlen**:

| Bereich | Grund |
|---|---|
| **Pläne** (`Plans/`) | Müssen immer ganz bleiben. Einzige zulässige Empfehlung: **Archivierung**, wenn abgearbeitet |
| **Setups** (`Setups/`) | Müssen self-contained sein — sie gehen an Menschen bzw. an fremde Agenten, die die übrigen Brain-Dateien **nicht** haben. Bewusste Redundanz ist hier Feature, kein Fehler |
| **Skills** (`Skills/`) | Werden als Ganzes gelesen oder installiert → self-contained lassen. Nur informativ im Report |
| `Maintenance/` | Reports, werden ohnehin überschrieben |

**Empfehlungen erzeugt die Analyse nur für:** `Projects/` (Split-Kandidaten), `Plans/`
(Archiv-Kandidaten), `Daily/` (Backfill-Stand), Hubs (Hygiene).

### Einzelfall-Ausschlüsse

Dateien, die trotz Überschreitung der Schwelle bewusst am Stück bleiben, gehören in diese Tabelle —
sonst werden sie bei jedem Lauf erneut gemeldet.

| Datei | Warum |
|---|---|
| *(noch keine)* | |

---

## Prüfungen

### 1. Kennzahlen (informativ)

Gesamtgröße, Verteilung pro Verzeichnis, Top-10 der größten Dateien (mit Kategorie heiß/kalt), Größe
der Navigationskette (`CLAUDE.md` → `_INDEX` → Bereichs-Hub → Unter-Hub). Token-Schätzung:
~3,5–4 Zeichen je Token bei deutschem Text.

### 2. Archiv-Kandidaten: abgearbeitete Pläne

```
Für jede Datei in Plans/ (außer Archiv/):
  → Status prüfen (alle Prüfpunkte ✓? Status-Feld? Hub-Eintrag?)
  → Abgearbeitet, aber nicht im Archiv → Archiv-Kandidat melden
```

Verschieben nur nach Bestätigung → [[Plans/Archiv/_Archiv]].

### 3. Split-Kandidaten: zu große Projektdateien

```
Für jede Datei in Projects/ (keine Hubs, keine Einzelfall-Ausschlüsse):
  → > 12 KB ODER (> 8 KB UND erkennbar gemischt aus „aktuellem Stand"
    und wachsender Historie) → Split-Kandidat melden
  → Vorschlag: welche Abschnitte in welche Teildateien
```

Die Durchführung übernimmt [[obsidian-project-split]] (nur nach Bestätigung) — diese Analyse
**meldet nur**.

### 4. Daily-Backfill-Stand

```
Für jede Tagesdatei bis einschließlich Vortag (der laufende Tag zählt nie):
  → Fehlt der „Gekürzt am"-Vermerk → ungekürzt, Kandidat für
    obsidian-daily-redundancy-check
→ Summe: N von M ungekürzt, geschätzte Ersparnis in KB
```

**Keine Karenzzeit** — der Redundancy-Check kürzt bis zum Vortag. Eine ungekürzte Tagesdatei ist ab
dem Folgetag ein legitimer Fund, nicht erst nach einer Woche.

### 5. Hub-Hygiene

- Überlange Zeilen in Hub-Tabellen (Details, die in die Zieldatei gehören)
- Überlange Highlight-Zeilen in den Monats-Hubs (Obergrenze 250 Zeichen → [[obsidian-daily-note]])

> ⚠️ **Bei überlangen Zeilen ist „gehört nach `Knowledge/`" fast immer die falsche Empfehlung.**
> Der Prüfstein ist nicht, ob der Inhalt thematisch woanders hinpasst, sondern ob er beim
> **Schreiben** präsent sein muss. Fallstricke und Konventionen sind Schreibzeit-Wissen: der Hub
> wird bei jedem Arbeiten gelesen, `Knowledge/` und `Decisions/` nur auf Nachfrage — dieselbe
> Erwägung wie *Kurzfassung bleibt im Hub* ([[obsidian-decision-scan]]). Richtige Empfehlung ist
> dann **Kürzen an Ort und Stelle**: Falle und Gegenmittel nach vorn, die Erzählung raus, **jeden
> Messwert behalten**. Eine Zeile knapp über dem Richtwert, die nur aus gemessenen Werten besteht,
> bleibt wie sie ist — ein Befund ohne Handlungsdruck, kein `action-required`.

> 📏 **Die Ersparnis einer Empfehlung vor dem Umsetzen neu messen.** Zwischen Empfehlung und
> Umsetzung liegen bei diesem Skill regelmäßig Tage — er ändert nichts selbst. Eine Zahl aus einem
> älteren Report kann durch zwischenzeitliche Kürzungen längst eingelöst sein; dann kostet die
> Umsetzung eine zusätzliche Datei und spart nichts. Die Zahl ist **immer** nachrechnungspflichtig,
> nicht nur im Verdachtsfall.

> ❌ **Eine verworfene Empfehlung wird im Report als ❌ mit Begründung vermerkt, nicht stillschweigend
> gestrichen** — und der Report geht damit auf `status: ok`, weil kein Handlungsbedarf mehr offen
> ist. Ohne den Vermerk taucht derselbe Vorschlag beim nächsten Lauf als neuer Fund wieder auf.

### 5b. Wenn eine Hub-Kürzung dann umgesetzt wird

Drei Punkte, die sonst genau dort Substanz kosten, wo gekürzt wird:

- 🔎 **Vor dem Kürzen einer Hub-Zeile prüfen, ob ihr Inhalt woanders steht — vault-weit, nicht nur in der Tagesdatei.** „Die Details stehen ja in der Daily" ist falsch: die Tagesdateien sind selbst gekürzt, die Details liegen in den Zieldateien. Belastbar ist ein Abgleich über alle **Code-Spans** der betroffenen Zeilen (Skript in [[obsidian-daily-redundancy-check]]) — es sind regelmäßig ein paar Werte darunter, die es **nur** in der Hub-Zeile gibt.
  - **Die Code-Span-Prüfung über *alle* Zeilen der Datei laufen lassen, nicht nur über die selbst angefassten.** Die Einschränkung „nur was ich anfasse" ist bei einer Hub-Kürzung wertlos, weil die nächste Kürzung dieselbe Datei trifft — Unikate in unberührten Zeilen sind dann verloren.
- ⚠️ **Erledigte Einträge sind nicht automatisch Historie.** Ein abgehakter Punkt kann den Blick nach vorn tragen („beim ersten Produktivlauf X gegenprüfen"). **Prüfstein ist nicht das `[x]`, sondern ob der Satz beim nächsten Arbeiten noch etwas vorgibt.** Solche Sätze bleiben als offene Punkte im Hub, während der Rest des Eintrags in die Historie wandert.
- 🛑 **Eine Widerlegung darf nicht als Nachtrag darunter stehen — die widerlegte Stelle trägt den Widerruf in sich selbst.** Behauptet ein Abschnitt weiter etwas Falsches, während die Korrektur zwei Abschnitte tiefer steht, liest jeder, der nur den ersten Abschnitt liest, eine falsche Aussage.
  - 🔎 **Ein überholter Stand steht selten an genau einer Stelle** — nach dem gemeldeten Fund die ganze Datei danach absuchen, nicht nur den Fund reparieren. Typischerweise trägt auch der Kopf-Blockquote — die allererste Aussage der Datei — noch den alten Stand. **Die Fassung in einer Handlungsliste ist die schlimmste Sorte:** ein überholter Status wird gelesen, eine überholte Anweisung wird *befolgt*. Reihenfolge beim Aufräumen: Kopf-Blockquote → Status-Abschnitte → offene Punkte/Checkboxen.

### 6. Rückfall-Prüfung gegen den Vorreport (Pflicht)

```
→ Vorreport lesen: welche Punkte galten als abgearbeitet?
→ Dieselben Dateien neu messen und mit dem damaligen Nachher-Wert vergleichen
→ Zurückgewachsen → als Rückfall melden, mit beiden Zahlen und der Ursache
```

**Kürzungen halten nicht von selbst.** Ohne den Abgleich mit dem Vorreport liest sich so ein Fund
wie ein neuer Kandidat statt wie ein Rückfall — und die eigentliche Frage („warum wächst es
zurück?") wird nie gestellt. Die Antwort ist meist strukturell: Chronik im Status-Abschnitt statt in
der Historie, Tagesinhalt in die Hub-Zeile kopiert statt zusammengefasst.

---

## Ablauf

0. **Datum messen** (Systemzeit, nicht aus dem Kontext)
1. **Messen:** Dateiliste mit Größen erheben, Navigationskette und Verzeichnis-Summen berechnen
2. **Prüfungen 2–6** durchführen — Inhalts-Checks per gezieltem Lesen, kein Voll-Read großer Dateien
3. **Report schreiben** und das Ergebnis zusammenfassen

## Report

`Brain/Maintenance/token-efficiency-report.md` **komplett überschreiben**:

```markdown
---
last-run: YYYY-MM-DD
status: ok | action-required
tags: [maintenance]
created: YYYY-MM-DD   # bestehendes Datum beibehalten, nicht auf das Lauf-Datum setzen
---

# Token Efficiency Report — YYYY-MM-DD

## Kennzahlen
Gesamt: N Dateien, N KB (~N Tokens) | Navigationskette: N KB
Top-Verzeichnisse: … | Top-Dateien: … (heiß/kalt)

## Archiv-Kandidaten (Pläne, abgearbeitet)
- Plans/XY_Plan.md (N KB) — alle Prüfpunkte ✓ → Plans/Archiv/

## Split-Kandidaten (Projektdateien)
- Projects/…/XY.md (N KB) — Vorschlag: XY/_XY.md + XY_Historie.md

## Daily-Backfill
N von M Tagesdateien ungekürzt (~N KB Potenzial)

## Hub-Hygiene
- …

## Rückfälle gegen den Vorreport
- …

[Keine Auffälligkeiten ✓]
```

**`status`-Regel:** `ok` wenn keine **offenen** Kandidaten, sonst `action-required`.

---

## Regeln

- **Nur lesen, nie ändern** — außer der Report-Datei
- Pläne, Setups und Skills **niemals** zur Kürzung, Deduplizierung oder zum Split empfehlen — bei Plänen ausschließlich die Archivierung
- Dateien verschieben (auch ins Archiv) nur nach Bestätigung
- Schwellwerte (12 KB / 8 KB) sind Richtwerte — bei Anpassung hier im Skill ändern
- Kalte Pfade nicht problematisieren

## Aufruf

> „Führe obsidian-token-efficiency-analysis aus"
> „Wie steht es um die Token-Effizienz des Brains?"
> „Gibt es Split- oder Archiv-Kandidaten?"
