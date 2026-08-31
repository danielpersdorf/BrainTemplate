---
tags: [agents, hub]
created: 2026-01-01
---
# Agents — Übersicht

> Sub-Agent-Definitionen. Der Vault ist die Source of Truth — von hier wird nach
> `.claude/agents/<name>.md` deployed (oder der Agent direkt mit dem Vault-Pfad beauftragt).

> 🔤 Konvention: Listen-Einträge in dieser Datei immer alphabetisch sortieren — neue Einträge an der korrekten Position einfügen, nicht unten anhängen.

## Agents

| Agent | Aufgabe |
|---|---|
| *(noch keiner)* | |

## Konventionen

- Dateiname = Agent-Name
- Frontmatter: `name`, `description`, `tools`
- Der System-Prompt beschreibt Rolle, Scope und Verhalten präzise — ein Sub-Agent hat den
  Session-Kontext nicht, alles Nötige muss in seiner Definition stehen

## Aufbau

```markdown
---
name: agent-name
description: Ein Satz — wann dieser Agent eingesetzt wird.
tools: Read, Grep, Glob, Edit
---

Du bist … .

## Scope
Was dieser Agent tut — und was ausdrücklich nicht.

## Ablauf
1. …

## Regeln
- …
```
