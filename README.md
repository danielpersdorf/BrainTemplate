# BrainTemplate — Second Brain für Claude-Sessions

Eine leere, sofort benutzbare Obsidian-Vault-Vorlage als **persistentes Gedächtnis für KI-Agenten**
(Claude Code, Claude Desktop, Codex, …). Sie enthält die Verzeichnisstruktur, alle Hub-Dateien,
die Navigations- und Schreibregeln sowie einen Satz Wartungs-Skills, mit denen sich der Vault
weitgehend selbst pflegt.

Verzeichnis leer, Regeln fertig — Inhalt kommt von dir.

## Wozu

Ein Agent vergisst alles nach jeder Session. Dieser Vault löst das: Entscheidungen, Projektstand,
Abläufe und Personen-Kontext liegen strukturiert auf der Platte und stehen beim nächsten Start
sofort wieder zur Verfügung. Weil es Markdown in einem Git-Repo ist, ist die Historie das
Sicherheitsnetz und der Vault gleichzeitig für Menschen lesbar.

## Struktur

```
BrainTemplate/
├── CLAUDE.md                   ← Navigations- und Schreibregeln (liest Claude Code automatisch)
├── AGENTS.md                   ← dieselben Regeln für andere Agenten (Codex, Cursor, …)
├── README.md                   ← diese Datei
└── Brain/
    ├── _INDEX.md               ← Einstiegspunkt, wird zuerst gelesen
    ├── Me.md                   ← Profil des Nutzers (Rolle, Stack, Arbeitsweise)
    ├── Agents/                 ← Sub-Agent-Definitionen
    ├── Daily/                  ← chronologisches Log, Tagesdateien in Monatsordnern
    ├── Decisions/              ← Entscheidungen mit Begründung und verworfenen Alternativen
    ├── Knowledge/              ← Fremdsysteme, Tools, Konzepte (kein eigener Code)
    ├── Maintenance/            ← Reports der Wartungs-Skills (werden überschrieben)
    ├── People/                 ← Personen-Kontext
    ├── Plans/                  ← Implementierungspläne und Checklisten (+ Archiv/)
    ├── Projects/               ← eine Datei je Projekt
    ├── Prompts/                ← wiederverwendbare Prompts
    ├── Setups/                 ← Infrastruktur-Dokumentation
    │   └── Obsidian/           ← Einrichtung des Vaults selbst (Setup-Anleitung)
    └── Skills/                 ← Abläufe („wie mache ich X")
        └── Obsidian/           ← die Wartungs-Skills des Vaults selbst
```

Jedes Verzeichnis hat eine `_`-Hub-Datei als Einstieg. Die Navigation ist bewusst flach:
`_INDEX` → Bereichs-Hub → Zieldatei. Hub-Listen sind alphabetisch sortiert, jede neue Datei
wird sofort in ihrem Hub verlinkt — **keine Datei ohne Hub-Eintrag**.

## Loslegen

> Wer es Schritt für Schritt mag — inklusive Obsidian-Installation, MCP-Anbindung an Claude und
> optionalem eigenem Git-Repo — folgt der ausführlichen Anleitung im Vault selbst:
> **[`Brain/Setups/Obsidian/obsidian-brain-setup-einfach.md`](Brain/Setups/Obsidian/obsidian-brain-setup-einfach.md)**.
> Sie setzt keine Git-Kenntnisse voraus. Die Kurzfassung:

1. Repo klonen und den Ordner in Obsidian als Vault öffnen (optional — alles funktioniert auch ohne Obsidian, es sind reine Markdown-Dateien).
2. `Brain/Me.md` ausfüllen — das ist die einzige Datei, die von Anfang an Inhalt braucht.
3. `CLAUDE.md` durchgehen und an die eigenen Gewohnheiten anpassen (Sprache, was der Agent eigenständig darf).
4. Ein Git-Remote setzen und einmal pushen — die Historie ist bei jeder Kürzung das Sicherheitsnetz.
5. Arbeiten. Der Agent legt Projekt-, Decision- und Daily-Dateien beim Arbeiten selbst an.

Optional, aber der eigentliche Gewinn: die Wartungs-Skills als tägliche/wöchentliche Routinen
einplanen (siehe unten).

## Zugriffswege

Der Vault ist eine Ordnerstruktur aus Markdown — jeder Agent mit Dateizugriff kann damit arbeiten.
Zusätzlich möglich:

| Weg | Wozu |
|---|---|
| Datei-Tools (Read/Edit/Grep) | Standardweg, am billigsten, keine Zusatzinstallation |
| Obsidian Local REST API + MCP | Backlinks, Frontmatter, Volltextsuche, Document Maps — Einrichtung: [`Brain/Setups/Obsidian/obsidian-brain-setup-einfach.md`](Brain/Setups/Obsidian/obsidian-brain-setup-einfach.md) |

Die Skills nennen beide Wege. Ohne MCP fallen nur die Backlink-basierten Abkürzungen weg — die
Skills beschreiben für jeden davon den lokalen Ersatz.

## Selbstpflege

Unter `Brain/Skills/Obsidian/` liegen Wartungs-Skills, die ihre Ergebnisse nach
`Maintenance/` schreiben:

| Skill | Was er tut | Ändert etwas? |
|---|---|---|
| `obsidian-daily-note` | Tageseintrag schreiben (Pfad, Struktur, Monats-Hub) | ja |
| `obsidian-daily-todos-verschieben` | offene TODOs auf den nächsten Tag übertragen | ja |
| `obsidian-brain-update` | Sessions auswerten und die Tagesdatei füllen | ja |
| `obsidian-daily-redundancy-check` | Wissen aus Dailies in die Wissensbasis ziehen, dann kürzen | ja (mit Gate) |
| `obsidian-frontmatter-repair` | fehlendes `tags`/`created` ergänzen | ja (nur additiv) |
| `obsidian-alphabetic-index-repair` | Hub-Listen alphabetisch sortieren | ja (nur Reihenfolge) |
| `obsidian-broken-links-report` | tote Wikilinks finden | nein |
| `obsidian-orphan-check` | Dateien ohne Backlink / ohne Hub-Eintrag finden | nein |
| `obsidian-decision-scan` | verbindliche Regeln außerhalb von `Decisions/` finden | nein |
| `obsidian-token-efficiency-analysis` | heiße Lese-Pfade vermessen, Split-/Archiv-Kandidaten melden | nein |
| `obsidian-project-split` | zu große Projektdatei in Hub + Teildateien aufteilen | ja (nur nach Bestätigung) |
| `obsidian-hub-anlegen` | neue Hub-Datei nach Template | ja |
| `obsidian-daily-commit-push` | Tages-Commit + Maintenance-Flag in die Daily | ja |
| `obsidian-vault-patch-fallstricke` | Stolperfallen beim Schreiben in den Vault | nein (Lesestoff) |

Ein Vorschlag für die Reihenfolge, wenn du sie als geplante Aufgaben einrichtest — erst die
inhaltsverändernden Läufe, dann die Reports, zuletzt der Commit:

```
19:15  obsidian-daily-redundancy-check
19:30  obsidian-frontmatter-repair
19:45  obsidian-alphabetic-index-repair
20:00  obsidian-broken-links-report
20:15  obsidian-orphan-check
20:45  obsidian-token-efficiency-analysis   (wöchentlich)
21:00  obsidian-daily-commit-push
```

Meldet ein Lauf etwas Auffälliges (`status: action-required`), setzt die Commit-Routine ein
`⚠ Maintenance`-Flag in die Tagesdatei des nächsten Arbeitstages — der Agent weist zu
Session-Beginn darauf hin. Kein Flag heißt „alles grün".

## Die Regeln, die den Unterschied machen

Alles Weitere steht in `CLAUDE.md`. Die vier, an denen ein Brain sonst zerfällt:

- **Keine Datei ohne Hub-Eintrag**, alphabetisch einsortiert — sonst wächst ein Haufen statt einer Struktur.
- **Dailies sind ein Log, kein Wissensspeicher.** Wissen wandert in Projects/Decisions/Skills, der Hergang bleibt im Tag und darf verschwinden.
- **Konvention nach `Decisions/` verlagern heißt nicht, sie aus dem Hub zu entfernen** — die Kurzfassung bleibt dort, wo beim Arbeiten gelesen wird.
- **Vor dem Vollschreiben einer Datei frisch lesen.** Ein Lesestand von vor zwei Stunden ist kein Schreibstand.

## Lizenz

Frei verwendbar. Fork, füll, ändere die Regeln, bis sie zu deiner Arbeit passen — die Struktur ist
ein Vorschlag, keine Vorschrift.
