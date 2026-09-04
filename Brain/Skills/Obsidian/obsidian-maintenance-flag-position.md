---
tags: [skill, obsidian, maintenance]
created: 2026-01-01
status: active
---
# Skill: obsidian-maintenance-flag-position

Wo genau im Kopf einer Tagesdatei das Maintenance-Flag steht, wenn dort schon ein Kürzungsvermerk liegt — und warum die Reihenfolge nicht beliebig ist.

Gehört zu [[obsidian-daily-commit-push]] (schreibt das Flag) und [[obsidian-daily-redundancy-check]] (setzt den Kürzungsvermerk). Dort steht die Kurzregel, hier die Begründung.

---

## Die Regel

Zwei Routinen schreiben in den Kopf einer Tagesdatei, zwischen Frontmatter und den ersten `---`-Trenner:

| Zeile | Wer schreibt sie | Was sie sagt |
|---|---|---|
| `> Gekürzt am YYYY-MM-DD per obsidian-daily-redundancy-check` | [[obsidian-daily-redundancy-check]] | **Zustand der Datei** — ihr Inhalt ist nicht mehr vollständig |
| `> ⚠ Maintenance YYYY-MM-DD: …` bzw. `> ✅ …` | [[obsidian-daily-commit-push]] | **Aufgabe für den Tag** — ein Report steht auf `action-required` |

Treffen beide zusammen:

1. **Der Kürzungsvermerk bleibt ganz oben**, unmittelbar hinter dem Frontmatter, unverändert.
2. **Das Maintenance-Flag kommt danach**, als **eigener** `>`-Block.
3. **Zwischen beiden eine Leerzeile** — sonst fasst Markdown sie zu einem einzigen Blockquote zusammen und die beiden Aussagen verschmelzen optisch zu einer.
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

---

## Warum diese Reihenfolge, nicht die andere

Weil die beiden Zeilen unterschiedlich lange gültig sind und Unterschiedliches vom Leser wollen.

Der **Kürzungsvermerk ist ein dauerhafter Zustandsvermerk der Datei**: er sagt „was du hier liest, ist nicht mehr alles, das Wissen steht in Projects/Decisions/Skills". Diese Aussage gilt, solange die Datei existiert, und sie **qualifiziert alles Folgende** — auch das Flag. Deshalb steht sie zuerst.

Das **Maintenance-Flag ist ein Arbeitsauftrag mit Verfallsdatum**: es zeigt auf einen Report, wird abgearbeitet und dann von `⚠` auf `✅` umgeschrieben. Es gehört zum Tagesgeschäft, nicht zum Zustand der Datei.

Steht das Flag oben, liest sich der Kopf falschherum: erst eine Aufgabe, dann die Einschränkung, unter der sie zu lesen ist. Beim Überfliegen gerät außerdem der Kürzungsvermerk aus dem Blick — und genau der ist die Information, die verhindert, dass jemand aus einer gekürzten Tagesdatei auf „da war nichts weiter" schließt.

---

## Warum das überhaupt aufgeschrieben gehört

⚠️ **Kein Wartungslauf prüft diese Reihenfolge.** Der Broken-Links-Report prüft Linkziele, der Orphan-Check Backlinks und Hub-Einträge, der Frontmatter-Repair das Frontmatter — die **Reihenfolge zweier Blockquotes darunter sieht keiner von ihnen an.**

Die Regel hat damit keinen automatischen Wächter; sie hängt allein daran, dass die schreibende Routine sie kennt. In einem gewachsenen Vault fallen die Abweichungen deshalb erst beim Hinsehen auf — und dann gleich stapelweise: vertauschte Reihenfolge in einigen Dateien, fehlende Leerzeile in weiteren. Dieselbe Ursache in beiden Fällen: der Kopf wurde als „irgendwo vor dem Trenner" behandelt statt als feste Struktur.

Wer die Kopfbereiche einmal aufräumt, prüft am besten beides zugleich — die Reihenfolge **und** die Leerzeilen. Nur eins von beidem zu korrigieren erzeugt einen zweiten Durchgang.

---

## Beim Umschreiben auf ✅ mitbeachten

- **Backticks um zitierte Link-Ziele erhalten.** Nennt die Flag-Zeile ein kaputtes Wikilink-Ziel, steht es als Inline-Code. Fallen die Backticks beim Umformulieren weg, erzeugt die abgeschlossene Tagesdatei beim nächsten Broken-Links-Lauf wieder eine Fundstelle — der Befund sieht dann wie neu aus.
- **Die Position ändert sich beim Umschreiben nicht.** `⚠` → `✅` ist ein Textwechsel in derselben Zeile, kein Anlass, den Block zu verschieben.
- Ist die Datei zwischenzeitlich gekürzt worden und trägt jetzt einen Kürzungsvermerk, den sie beim Schreiben des Flags noch nicht hatte: Vermerk nach oben, Flag darunter — das ist der Fall, in dem die Reihenfolge am häufigsten kippt.
