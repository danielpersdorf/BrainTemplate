---
tags: [skill, obsidian, tooling]
created: 2026-01-01
---
# Skill: obsidian-vault-patch-fallstricke

Stolperfallen beim **Schreiben in den Vault** — Klassen von Fehlern, die alle dieselbe Eigenschaft
haben: nichts schlägt fehl, und der Schaden fällt erst Tage später auf. Vor einer Serie von
Vault-Schreibvorgängen mitlesen. Ergänzt [[_OBSIDIAN]], Abschnitt „Werkzeug-Fallen".

Die Beispiele beziehen sich auf ein Patch-Werkzeug mit Abschnitts-Zielen (`heading`, `frontmatter`),
wie es die Obsidian-REST-API bietet. Die Fehlerklassen gelten aber für jedes Werkzeug, das
**Abschnitte ersetzt oder ganze Dateien schreibt** — auch für ein einfaches Lesen–Ersetzen–Schreiben.

---

## 1. Ein Replace auf die H1 löscht den ganzen Dateikörper

Ein Abschnitts-`replace` auf eine **H1** ersetzt den Inhalt bis zur nächsten gleich- oder
höherrangigen Überschrift — bei Dateien mit genau einer H1 also **alles**, inklusive sämtlicher
`##`-Unterabschnitte. Die Unterüberschriften sind danach weg und per Patch nicht mehr erreichbar
(„target not found").

**Typischer Schaden:** ein Statuszeilen-Update kürzt eine 3,6-KB-Datei auf 0,5 KB. Wiederherstellung
nur aus der Git-Historie oder aus dem Sitzungskontext.

**Regel:** Für Text direkt unter einer H1 nie ein Replace auf die H1 — stattdessen die ganze Datei
lesen und schreiben (siehe Punkt 4) oder gezielt eine `##`-Unterüberschrift patchen.

---

## 2. Ein Hub-Patch verschluckt die Indextabelle

Besteht eine Hub-Datei aus **Konventions-Blockquotes und darunter einer Indextabelle**, nimmt ein
Einfügen „am Ende der Blockquote-Liste" die Tabelle still mit — der eingefügte Text ersetzt sie.

**Typischer Schaden:** Das Einfügen einer neuen Konventionszeile in einen Hub löscht dessen
Tabellen-Abschnitt. Folge: alle darunterhängenden Dateien sind nicht mehr an der Navigationskette,
und **mehrere Maintenance-Reports melden am selben Abend `action-required` aus derselben einen
Ursache**.

**Regel:** Nach jedem Hub-Patch gegen `git diff` prüfen, ob **nur** die gewollte Zeile dazukam.
Bei Hubs mit Tabelle die Tabelle nie als „Rest der Datei" behandeln.

---

## 3. Frontmatter-Arrays werden zu Strings

Ein Patch mit `targetType: frontmatter` serialisiert Arrays als gequoteten String
(`tags: '["maintenance"]'` statt `tags: [maintenance]`). Obsidian erkennt einen String nicht als
Tags. Ein „JSON"-Flag im Werkzeug ist **kein verlässlicher Schutz** — es hilft mal und mal nicht.

**Regel:** Frontmatter immer im **kompletten Schreib-Payload** mitgeben, nie nachträglich patchen.
Nach dem Schreiben gegenprüfen, dass wirklich ein YAML-Array dasteht. → [[obsidian-frontmatter-repair]]

---

## 4. Die empfohlene Alternative hat ihre eigene Falle: Vollschreiben mit veraltetem Lesestand

Punkt 1 nennt „ganze Datei lesen und schreiben" als sicheren Ausweg. Das gilt nur, wenn der
**Lesestand frisch** ist. Ein Vollschreiben überschreibt stillschweigend alles, was seit dem Lesen
dazugekommen ist — ohne Konflikt und ohne Warnung.

**Typischer Schaden:** Eine Tagesdatei wird am Sitzungsende mit dem Lesestand vom Sitzungs**beginn**
geschrieben. Dazwischen liegen Stunden und vier Abschnitte. Was committet war, kommt aus der
Historie zurück; was nicht committet war, ist endgültig weg. Der Commit danach trägt sogar noch die
Überschrift der verlorenen Abschnitte, enthält aber schon die überschriebene Fassung.

**Regeln:**

- Vor jedem Vollschreiben die Datei **unmittelbar davor** erneut lesen. Ein Lesestand vom Sitzungsbeginn ist nach Stunden kein Schreibstand.
- Für reines Hinzufügen `append`/`prepend` auf eine `##`-Überschrift verwenden — das kann nichts überschreiben und ist der Normalfall.
- Muss doch die ganze Datei geschrieben werden: vorher eine Kopie ziehen und hinterher `diff` gegen die Kopie fahren. Dann ist mechanisch belegt, dass nur die gewollte Änderung drin ist.
- **Rettbar ist nur, was committet ist.** Nach einem Schreibvorgang zeitnah committen.

---

## 5. Zielauflösung: eine `##`-Überschrift wird nicht über ihren nackten Text gefunden

Ein Abschnitts-Patch braucht den **verschachtelten Pfad** mit dem H1-Titel davor
(`H1-Titel::Unterabschnitt`). Ohne ihn kommt „target not found" — und genau das verleitet dazu, auf
die H1 auszuweichen und damit in Falle 1 zu laufen.

**Regel:** Vorher die Gliederung der Datei holen (Document Map), die liefert die Pfade fertig.

---

## Die Meta-Lektion

Diese Regeln werden nicht dadurch verletzt, dass jemand sie nicht kennt, sondern dadurch, dass diese
Datei **nicht gelesen wurde**. Vor einer Serie von Vault-Schreibvorgängen gehört sie mitgelesen —
nicht erst nach dem Schaden.
