---
tags: [skill, obsidian, maintenance]
created: 2026-01-01
status: active
---
# Skill: obsidian-maintenance-flag-position

Wo ein Maintenance-Befund steht, nachdem ihn eine Abendroutine erzeugt hat — in der globalen
TODO-Liste. Dazu die Kopf-Reihenfolge einer Tagesdatei, falls ein Vault noch Befunde aus dem älteren
Modell trägt, in dem sie als Flag in der Daily standen.

Gehört zu [[obsidian-daily-commit-push]] (Schritt 1 schreibt den Eintrag),
[[obsidian-todos-liste-pflegen]] (führt die Liste) und [[obsidian-daily-redundancy-check]] (setzt
den Kürzungsvermerk). Dort steht die Kurzregel, hier die Begründung.

---

## Die geltende Regel

Ein Report unter `Brain/Maintenance/` mit `status: action-required` wird zu **einem offenen Punkt
pro Report** im ersten Block von `Brain/Daily/TODOs.md`:

```markdown
## Brain-Maintenance (BrainWork)

- [ ] **orphan-check YYYY-MM-DD** — 2 Dateien ohne Hub-Eintrag → `Maintenance/orphan-check-report.md`
```

1. **Der Block `## Brain-Maintenance (BrainWork)` steht immer ganz oben** in `TODOs.md`, vor den
   Prio-Blöcken — die Gruppenreihenfolge ist vorgegeben und wird nicht umsortiert
   ([[obsidian-todos-liste-pflegen]]).
2. **Ein Punkt pro Report**, nicht pro Abendlauf: die Befunde werden einzeln erledigt.
3. **Das Laufdatum steht im Punkt**, nicht das Datum des Eintragens.
4. **Erledigt heißt `- [x]` mit Datum**, nicht löschen; ab dem sechsten erledigten Punkt der
   Gesamtliste wandern die ältesten ins Archiv.
5. **Ein wiedergemeldeter Befund aktualisiert den bestehenden Punkt**, er legt keinen zweiten an.

### Warum die TODO-Liste der richtige Ort ist

Der Befund ist ein **Arbeitsauftrag**, und Arbeitsaufträge gehören dorthin, wo alle anderen stehen.
Liegt er in einer Tagesdatei, liegt er in einer Datei, die einen *Tag* beschreibt: zum Erledigen
muss man ihn dort finden, und bleibt er liegen, wandert er nicht mit — er versinkt mit dem Tag. Die
Liste dagegen hält ihn sichtbar, bis er abgehakt ist, neben den übrigen offenen Punkten.

Der Nebeneffekt ist größer, als er aussieht: **der Ort existiert immer.** Eine Regel, die den Befund
in eine Tagesdatei schreibt, muss erst dafür sorgen, dass es die Zieldatei gibt — nächster
Arbeitstag statt „morgen", notfalls Tagesdatei anlegen, Monats-Hub nicht vergessen. Genau daraus
entsteht der teuerste Fehlermodus dieses Modells (s. unten). `TODOs.md` existiert und hat ihren
Hub-Eintrag; die halbe Regel fällt damit ersatzlos weg.

⚠️ **Eine Falle ist dafür schärfer:** `TODOs.md` wird dauerhaft gelesen, eine abgeschlossene
Tagesdatei nicht. Ein Wikilink-Ziel, das im Punkt zitiert wird, gehört deshalb erst recht in
Inline-Code (`` `[[Ziel]]` ``) — sonst meldet der nächste Broken-Links-Lauf die TODO-Liste selbst
als Fundstelle.

### Was der Ort in der Tagesdatei gekostet hat

Die Regel musste sicherstellen, dass die Zieldatei existiert. Eine Fassung, die den Befund in
„heute" schrieb und dafür notfalls eine Tagesdatei anlegte, erzeugt **Tagesdateien, die nur ihren
eigenen Befund enthalten**. ⚠️ Die Kette **füttert sich dabei selbst**: jede so angelegte Datei ist
am Folgeabend ein neuer Orphan-Check-Befund („Tagesdatei ohne Hub-Eintrag"), weshalb derselbe Fund
über eine Abwesenheit hinweg stetig wächst und Lauf um Lauf gemeldet wird. Ein Nebenbefund kommt im
Token-Efficiency-Report dazu („Alt-Dateien ohne Kürzungsvermerk, reine Stubs").

Die Gegenmaßnahmen dazu — Zieltag = nächster Arbeitstag, Befunde einer Abwesenheit in einer Zeile
bündeln, einen Eintrag zurückholen, wenn ein übersprungener Tag doch zum Arbeitstag wird — waren
allesamt Reparaturen an der **Ortswahl**, nicht am Befund. Mit der TODO-Liste fallen sie ersatzlos
weg. Das ist der Grund, die Geschichte hier stehen zu lassen: sie erklärt, warum die geltende Regel
so kurz ist.

---

## Bestandsregel: Flags im Kopf einer Tagesdatei

Trägt ein Vault noch Tagesdateien mit `⚠ Maintenance`-Flags aus dem älteren Modell, bleiben sie
stehen — sie sind Teil der jeweiligen Tagesgeschichte. **Nicht migrieren:** sonst steht derselbe
Befund an zwei Stellen, und die alten Tage lesen sich dauerhaft wie offene Baustellen. Ein offenes
Bestands-Flag wird **dort** erledigt (`⚠` → `✅`), nicht umgezogen.

Wer eine solche Datei anfasst, hält sich an die ursprüngliche Kopf-Reihenfolge. Zwei Routinen
schreiben zwischen Frontmatter und den ersten `---`-Trenner:

| Zeile | Wer schreibt sie | Was sie sagt |
|---|---|---|
| `> Gekürzt am YYYY-MM-DD per obsidian-daily-redundancy-check` | [[obsidian-daily-redundancy-check]] | **Zustand der Datei** — ihr Inhalt ist nicht mehr vollständig |
| `> ⚠ Maintenance YYYY-MM-DD: …` bzw. `> ✅ …` | Bestand aus dem älteren Modell | **Aufgabe für den Tag** — ein Report steht auf `action-required` |

Treffen beide zusammen:

1. **Der Kürzungsvermerk bleibt ganz oben**, unmittelbar hinter dem Frontmatter, unverändert.
2. **Das Maintenance-Flag kommt danach**, als **eigener** `>`-Block.
3. **Zwischen beiden eine Leerzeile** — sonst fasst Markdown sie zu einem einzigen Blockquote
   zusammen und die beiden Aussagen verschmelzen optisch zu einer.
4. Auch **zwischen Flag und dem folgenden `---`** eine Leerzeile.

```markdown
---
tags: [daily]
created: YYYY-MM-DD
---
> Gekürzt am YYYY-MM-DD per obsidian-daily-redundancy-check

> ✅ Maintenance YYYY-MM-DD (erledigt am YYYY-MM-DD): token-efficiency — …

---

# Erstes Thema
```

**Backticks um zitierte Link-Ziele erhalten.** Nennt die Flag-Zeile ein kaputtes Wikilink-Ziel,
steht es als Inline-Code. Fallen die Backticks beim Umformulieren weg, erzeugt die abgeschlossene
Tagesdatei beim nächsten Broken-Links-Lauf wieder eine Fundstelle — der Befund sieht dann wie neu
aus. **Die Position ändert sich beim Umschreiben nicht**: `⚠` → `✅` ist ein Textwechsel in
derselben Zeile, kein Anlass, den Block zu verschieben.

### Warum diese Reihenfolge, nicht die andere

Weil die beiden Zeilen unterschiedlich lange gültig sind und Unterschiedliches vom Leser wollen.

Der **Kürzungsvermerk ist ein dauerhafter Zustandsvermerk der Datei**: er sagt „was du hier liest,
ist nicht mehr alles, das Wissen steht in Projects/Decisions/Skills". Diese Aussage gilt, solange
die Datei existiert, und sie **qualifiziert alles Folgende** — auch das Flag. Deshalb steht sie
zuerst.

Das **Maintenance-Flag ist ein Arbeitsauftrag mit Verfallsdatum**: es zeigt auf einen Report, wird
abgearbeitet und dann umgeschrieben. Es gehört zum Tagesgeschäft, nicht zum Zustand der Datei.
Genau diese Beobachtung hat den Ort wechseln lassen: was ein Arbeitsauftrag ist, gehört zu den
Arbeitsaufträgen.

Steht das Flag oben, liest sich der Kopf falschherum: erst eine Aufgabe, dann die Einschränkung,
unter der sie zu lesen ist. Beim Überfliegen gerät außerdem der Kürzungsvermerk aus dem Blick — und
genau der verhindert, dass jemand aus einer gekürzten Tagesdatei auf „da war nichts weiter"
schließt.

---

## Warum das überhaupt aufgeschrieben gehört

⚠️ **Kein Wartungslauf prüft diese Regeln.** Der Broken-Links-Report prüft Linkziele, der
Orphan-Check Backlinks und Hub-Einträge, der Frontmatter-Repair das Frontmatter — weder die
Reihenfolge zweier Blockquotes noch die Frage, ob ein Maintenance-Punkt im obersten Block der
TODO-Liste steht, sieht einer von ihnen an.

Die Regel hat damit keinen automatischen Wächter; sie hängt allein daran, dass die schreibende
Routine sie kennt. In einem gewachsenen Vault fallen Abweichungen erst beim Hinsehen auf — und dann
gleich stapelweise: vertauschte Reihenfolge in einigen Dateien, fehlende Leerzeile in weiteren.
Dieselbe Ursache in beiden Fällen: der Kopf wurde als „irgendwo vor dem Trenner" behandelt statt als
feste Struktur. Wer die Kopfbereiche einmal aufräumt, prüft am besten beides zugleich — die
Reihenfolge **und** die Leerzeilen. Nur eins von beidem zu korrigieren erzeugt einen zweiten
Durchgang.
