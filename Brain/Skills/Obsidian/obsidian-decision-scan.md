---
tags: [skill, obsidian, maintenance]
created: 2026-01-01
status: active
---
# Skill: obsidian-decision-scan

Findet **verbindliche Regeln und Entscheidungen, die außerhalb von `Decisions/` stehen** — typisch
in Projekt- und Hub-Dateien, dort notiert, wo sie entstanden sind. Gibt einen Report aus; umgezogen
wird nur nach ausdrücklicher Bestätigung.

Warum das nötig ist: Eine Konvention, die nur im Projekt-Hub steht, findet man nur, wenn man das
Projekt schon kennt. Wer aus einem anderen Projekt kommt oder eine Warum-Frage hat, landet in
`Decisions/` — und findet dort nichts.

> ⚙️ **Der automatische Lauf führt nur Schritt 0–3 plus „Report speichern" aus.** Schritt 4 (Umzug)
> braucht ausdrückliche Bestätigung und entfällt dort ersatzlos — geschrieben wird ausschließlich
> `Maintenance/decision-scan-report.md`.
>
> **Rhythmus: wöchentlich.** Konventionen entstehen langsamer als tote Links. Der Lauf gehört
> zeitlich **vor** die übrige Maintenance-Kette und damit weit vor den Tages-Commit, der den Report
> mitnimmt.

---

## Ablauf

### Schritt 0 — Datum messen

**Erste Handlung jedes Laufs, vor dem ersten Lesen.** Dieses Datum gilt für `last-run`, den
Report-Titel, jeden Umzugsvermerk und das `created` neu angelegter Dateien. **Nicht aus dem
Session-Kontext ableiten** — der kann daneben liegen, und der Fehler ist still: ein einziger falsch
gemessener Lauf datiert am Ende ein Dutzend Dateien falsch. Läuft der Task an einem anderen Tag als
geplant, gilt das reale Datum, und der Report nennt den Versatz.

### Schritt 1 — Kandidaten einsammeln

Zwei Durchgänge über `Brain/`, **ohne** `Decisions/`, `Maintenance/`, `Daily/`,
`Plans/Archiv/`:

**A) Überschriften** — der Haupttreffer:

```
(?i)^#{2,4}.*(konvention|entscheidung|regel|vorgabe|standard|pattern|pflicht|policy|namens)
```

> Das Muster nie verengen, eher erweitern. Fehlt auch nur ein Begriff (typisch: `pattern`), fallen
> starke Kandidaten stillschweigend heraus.

**B) Fließtext-Signale** — fängt Regeln ohne eigene Überschrift:

```
(?i)(immer|nie(mals)?|grundsätzlich|verbindlich|Pflicht|verworfen|Vorgabe|entschieden|Merksatz)
```

Treffer aus B nur weiterverfolgen, wenn der Absatz eine **allgemeine** Aussage macht — nicht, wenn
er einen Einzelvorfall beschreibt.

### Schritt 2 — Bewerten

Ein Fund gehört nach `Decisions/`, wenn **mindestens zwei** davon zutreffen:

| Kriterium | Beispiel |
|---|---|
| gilt über den aktuellen Fall hinaus | „für alle Projekte dieser Art" |
| es gab eine **verworfene Alternative** | Weg A statt Weg B, und warum B ausschied |
| es steckt eine Begründung oder ein Vorfall dahinter | „weil die Bibliothek stillschweigend umkodiert" |
| jemand hat es **entschieden** | eine benannte Vorgabe, kein gewachsener Zustand |

**Bleibt, wo es ist:**

- **Ablauf / „wie mache ich X"** → gehört nach `Skills/`, nicht nach `Decisions/`
- **reine Mechanik-Beschreibung** („die Puffer-Tabelle markiert exportierte Sätze") → Projektnotiz
- **Fremdsystem-Verhalten** → `Knowledge/`
- **Hub-Sortierkonventionen** — die stehen absichtlich in jedem Hub
- **`Regeln`-Abschnitte innerhalb von Skill-Dateien** — sie gehören zum Ablauf
- **Archivierte Pläne** — konservierte Historie, wird nicht mehr angefasst
- **Regeln, die genau ein Dokument bei genau einer Person betreffen** — kein Geltungsbereich über den Einzelfall hinaus

### Dauer-Ausschlüsse

Geprüfte Fundstellen, die bewusst bleiben, wo sie sind, gehören in eine Tabelle **hier im Skill** —
und **nicht erneut in den Report**, auch nicht als „geprüft und bewusst gelassen". Sonst wächst der
Report mit Befunden, die keine sind.

| Fundstelle | Warum ausgeschlossen |
|---|---|
| *(noch keine)* | |

### Schritt 3 — Report ausgeben

```
DECISION SCAN
────────────────────────────────────
Dateien geprüft: N
Kandidaten: X (stark: A · mittel: B)

STARK — verbindliche Regel mit Begründung
  Projects/Libraries/auth.md:51  „Passwörter"
    → Kriterien: übergreifend, verworfene Alternative, Vorfall
    → Ziel: Decisions/Auth_decisions.md (neu)

MITTEL — Konvention ohne dokumentierte Alternative
  Projects/WebApp/legacy.md:94  „Konventionen"
    → dreifach dupliziert (legacy, tools, importer)
    → Ziel: Decisions/Legacy_decisions.md

Keine Änderungen vorgenommen.
```

Mehrfach auftauchende Regeln (dieselbe Vorgabe in drei Notizen) **immer als eine Zeile mit allen
Fundstellen** melden — die Dublette ist das eigentliche Argument für den Umzug.

**Starke Funde ausführlich, mittlere knapp.** Auf dem Bildschirm zählen die starken Kandidaten
einzeln; die mittleren dürfen zu einer Sammelzeile zusammengefasst werden („11 mittlere — Details im
Report"). Vollständig aufgeführt werden sie **in der Report-Datei**, damit man bei Bedarf nachsieht,
statt sie im Chat abzuarbeiten.

---

## Schritt 4 — Umzug (nur nach Bestätigung)

> ⚠️ **Die wichtigste Regel dieses Skills: Ein Umzug ist kein Verschieben.**
> **Die Kurzfassung bleibt im Hub, nach `Decisions/` wandert die Begründung.**
> `Decisions/` wird nur auf Nachfrage gelesen, die Hub-Datei bei jedem Arbeiten — eine Regel, die
> nur noch in `Decisions/` steht, greift beim Schreiben nicht mehr. Zu dünn geratene Stubs müssen
> anschließend zurückgeholt werden; das ist der teuerste Fehler dieses Skills.

Pro bestätigtem Fund:

1. **Zielort wählen** — bestehende `PROJEKT_decisions.md` bevorzugen; neue Datei nur, wenn keine passt (Frontmatter mit `tags`, `created`, `projekt`)
2. **Abschnitt in der Decisions-Datei anlegen:** Regel, Begründung, verworfene Alternative, Vorfall/Beleg, Datum in der Überschrift (`## Thema (YYYY-MM-DD)`)
3. **Quelle kürzen, nicht leeren:** Überschrift bleibt, die Regel bleibt in Kurzform, der Verweis `→ [[…_decisions]]` kommt dazu. **Test:** *Könnte jemand allein mit dem Hub-Text die Regel korrekt befolgen?* Nein → zu kurz gekürzt.
4. **Hub `_Decisions.md`** — neue Datei alphabetisch einsortieren, bei erweiterten Dateien die Beschreibungsspalte nachziehen
5. **Nachscan** mit [[obsidian-broken-links-report]] — Umzüge erzeugen neue Wikilinks

⚠️ Bei Abschnitts-Anchors (`[[Datei#Überschrift]]`) die Zielüberschrift **zeichengenau** gegenprüfen —
der Broken-Links-Report schneidet Anchors ab und meldet einen falschen Anchor **nicht**.

⚠️ **Beim Verschieben einer Notiz auch die Reports in `Maintenance/` nach Klartext durchsuchen**,
nicht nur nach Wikilinks. Ein Report kann die alte Platzierung ausdrücklich als *richtig* beurteilen;
bleibt der Satz stehen, bestätigt der nächste Lauf die Fehlplatzierung erneut und der Umzug wird
rückwärts begründet. Solche Sätze stehen als Prosa da — ein Backlink-Array findet sie nicht.

---

## Report speichern

`Brain/Maintenance/decision-scan-report.md` **komplett überschreiben**, Aufbau und
Frontmatter-Regeln wie bei [[obsidian-broken-links-report]]:

- `tags: [maintenance]` als echtes YAML-Array
- `created` aus dem alten Frontmatter übernehmen, nur beim ersten Lauf das heutige Datum
- komplettes Frontmatter im Schreib-Payload — nichts nachreichen
- **`status: action-required` nur, wenn mindestens ein *starker* Kandidat offen ist.** Mittlere Kandidaten allein → `status: ok`, mit einer Zeile im Report, wie viele es sind.

  Grund: `action-required` erzeugt über die Commit-Routine das `⚠ Maintenance`-Flag und damit einen
  Hinweis zu Session-Beginn. Mittlere Funde sind gut zu wissen, aber kein Handlungsdruck — sie jede
  Woche als Warnung zu melden, **nutzt das Flag ab**. Ein wöchentlicher Lauf trifft ohnehin
  überwiegend dieselben bewusst liegengelassenen Kandidaten wieder; die stehen dauerhaft im Report
  und brauchen kein Flag.
- Ein bestätigter und umgezogener starker Fund zählt nicht mehr als offen — sind alle starken abgearbeitet, geht der Status auf `ok`, auch wenn mittlere übrig sind

---

## Regeln

- **Standardmäßig nur lesen** — geschrieben wird nur die Report-Datei; Umzüge nur nach ausdrücklicher Bestätigung
- **Nie ersatzlos aus der Quelle löschen** (siehe Kasten in Schritt 4)
- `Knowledge/` wird mitgeprüft, aber nur auf **Fehlplatzierung** (eigener Code, eigene Repos) — Fremdsystem-Fakten sind dort richtig
- Ein Fund ist **kein Fehler** — der Nutzer entscheidet, ob umziehen oder liegenlassen
- Bei Lesefehler: Datei überspringen, als `⚠ Lesefehler` melden, `status: action-required`

## Aufruf

> „Führe obsidian-decision-scan aus"
> „Gibt es Konventionen, die in die Decisions gehören?"
> „Wo stehen Regeln außerhalb von Decisions?"
