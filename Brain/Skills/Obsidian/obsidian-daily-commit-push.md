---
tags: [skill, obsidian, git]
created: 2026-01-01
status: active
---
# Skill: obsidian-daily-commit-push

Täglicher Git-Commit des Vaults — und davor das **Maintenance-Flag** in die Tagesdatei, wenn ein
Wartungslauf etwas Auffälliges gemeldet hat. Läuft als letzte Routine des Tages, nach den
Maintenance-Skills, damit deren Reports mit im Commit landen.

---

## Schritt 0 — Datum messen

Systemzeit abfragen. Das Flag trägt dieses Datum, und der Zieltag wird daraus abgeleitet.

## Schritt 1 — Maintenance-Anomalie-Flag

Alle Report-Dateien unter `Brain/Maintenance/` auf ihr `status`-Frontmatter prüfen:

```powershell
Get-ChildItem "$vault\Brain\Maintenance" -Filter *.md |
  Select-String -Pattern '^status:\s*action-required' -List
```

- **Keine Treffer** → nichts tun. Grüne Tage bleiben rauschfrei — kein „lief durch"-Eintrag, das steht im Report-Frontmatter.
- **Mindestens ein `action-required`** → **eine** Zeile an den **Anfang der Tagesdatei des nächsten Arbeitstages** einfügen (direkt nach dem Frontmatter bzw. hinter einem Kürzungsvermerk, **vor** dem `# TODO`-Block):

```markdown
> ⚠ Maintenance YYYY-MM-DD: orphan-check action-required (2 fehlende Hub-Einträge) · token-efficiency action-required (1 Split-Kandidat) → siehe Maintenance/
```

### Regeln für das Flag

- **Nur Anomalien melden, nie den Normalzustand** — es ist ein Aufmerksamkeits-Signal, kein Protokoll.
- Kurzgrund aus dem jeweiligen Report ziehen (die Kopfzeile genügt).
- **Das Flag bleibt dauerhaft in der Tagesdatei.** Es wird beim Kürzen durch den [[obsidian-daily-redundancy-check]] **nicht** weggelassen, nicht umformuliert und nicht gelöscht — nur ggf. von `⚠` auf `✅` umgeschrieben. **Eine fehlende Flag-Zeile heißt „grüner Tag", nie „wurde gekürzt".**
- **Zieltag ist der nächste Arbeitstag, nicht wörtlich „morgen".** Der Freitagslauf schreibt in die Montags-Datei — eine Wochenenddatei wird nie geöffnet, das Flag wäre bis Montag unsichtbar. Feiertage und angekündigte Abwesenheiten genauso behandeln.
  - **Wird ein übersprungener Tag doch zum Arbeitstag, wandert das Flag zurück in diesen Tag** — mitsamt der Notiz über seine Abarbeitung. Sonst steht der Befund im einen Tag und die Erledigung im anderen. Der Rest des ursprünglichen Zieltags bleibt unangetastet.
- **Das Flag trägt das Datum seines Laufs, nicht das der Datei:** `> ⚠ Maintenance 2026-08-20: …` steht in `2026-08-21.md`. Der Text sagt, welcher Abendlauf den Befund erzeugt hat, die Datei sagt, wo er abgearbeitet wird. Ohne diese Trennung driften Flag-Datum und Dateiname stillschweigend auseinander.
- Existiert die Tagesdatei des Zieltages noch nicht: minimale Datei nach [[obsidian-daily-note]] anlegen (`created` = Zieltag) und nur das Flag hineinschreiben. **Zeile im Monats-Hub nicht vergessen** — eine vorab angelegte Tagesdatei ohne Hub-Eintrag ist am Folgeabend ein Orphan-Check-Befund.
- 🚫 **Niemals eine Tagesdatei für einen Nicht-Arbeitstag anlegen.** Eine Vorgängerfassung dieser Regel schrieb das Flag in „heute" und legte dafür notfalls eine Datei an — Ergebnis war ein Dutzend Tagesdateien, die nur ihr eigenes Flag enthielten. ⚠️ Die Kette **füttert sich dabei selbst**: jede so angelegte Datei ist am Folgeabend ein neuer Orphan-Check-Befund, weshalb derselbe Fund über eine Abwesenheit hinweg stetig wächst und Lauf um Lauf gemeldet wird.
- **Trägt der Zieltag schon ein Flag aus einem früheren Lauf derselben Abwesenheit, die bestehende Zeile erweitern — keine zweite anhängen.** Bei längerer Abwesenheit zielt jeder Abendlauf auf **dieselbe** Datei; ein Zweiwochenurlaub erzeugt sonst zehn Flag-Zeilen im Rückkehrtag. Zielform ist **eine** Zeile über den Zeitraum: `> ⚠ Maintenance der Abendläufe YYYY-MM-DD bis YYYY-MM-DD: …`, je Befund einmal genannt, mit dem Verlauf statt einer Wiederholung („von 1 auf 8 angewachsen").
- **Flag und Report müssen dasselbe sagen.** Ein Report, der im Flag steht, muss selbst `status: action-required` und den Fund tragen — das Flag wird aus dem Frontmatter abgeleitet, nie aus Wissen, das nur im Lauf vorliegt. Fällt beim Schreiben auf, dass ein Report nicht nachgezogen wurde: erst den Report schreiben, dann flaggen.
- **Ein abgearbeitetes Flag wird umgeschrieben, nicht liegengelassen:**
  `> ✅ Maintenance <Datum> (erledigt am <Datum>): <was war> — <was wurde getan>, Report `ok`.`
  Sonst lesen sich Wochen später alte Tage wie offene Baustellen.
  - ⚠️ **Beim Umschreiben die Backticks um zitierte Link-Ziele erhalten.** Nennt die Flag-Zeile ein kaputtes Wikilink-Ziel, steht es als Inline-Code da. Fallen die Backticks beim Umformulieren weg, erzeugt die *abgeschlossene* Tagesdatei beim nächsten Broken-Links-Lauf wieder eine Fundstelle, und der Befund sieht wie neu aus.

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
> Gegenteil — dort nur die eigenen Zieldateien einzeln stagen (→ [[_Obsidian]]).

## Schritt 6 — Commit-Nachricht

Format `brain: <kurze Beschreibung>`, z.B.:

- `brain: WebApp Deploy-Flow dokumentiert`
- `brain: Daily 2026-06-18 aktualisiert`
- `brain: 3 Notizen bearbeitet (Projects, Decisions, Daily)`

⚠️ Nachricht in eine Datei schreiben und `git commit -F` nehmen, nicht `-m` mit Here-String — und
die Nachrichtendatei **ohne BOM** schreiben. → [[_Obsidian]]

## Schritt 7 — Commit und Push

```powershell
git -C $vault commit -F $msgFile
git -C $vault push origin main
```

---

## Abschluss-Ausgabe

Commit-Hash und Nachricht · Push-Status · Anzahl geänderter Dateien · ob ein Maintenance-Flag
gesetzt wurde.
