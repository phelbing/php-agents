# php-agents

8 Subagents mit Modell-Routing (haiku, sonnet, opus) für PHP und Doctrine: Code-Suche, Tests, Review, Migrationen, Sicherheit und Performance. Dazu PHP-Konventionen und ein Skill mit Entwurfsmustern. Funktioniert mit jedem PHP-Framework. Für Symfony-Projekte ergänzt `symfony-agents` (eigenes Repo).

## Installation

`php-agents` baut auf `developer-workflow-agents` auf. Die Marketplaces der Abhängigkeiten müssen vorher hinzugefügt sein, sonst lädt das Plugin nicht:

```bash
claude plugin marketplace add <owner>/developer-workflow-agents
claude plugin marketplace add <owner>/php-agents
claude plugin install php-agents@php-agents
```

Die Abhängigkeit `developer-workflow-agents` wird dabei automatisch mitinstalliert. Danach in Claude Code `/agents` ausführen. Die Agents heißen mit Plugin-Präfix, z. B. `php-agents:php-code-reviewer`.

## Enthalten

| Modell | Agents |
|---|---|
| opus | php-security-auditor |
| sonnet | php-implementer, php-test-writer, php-code-reviewer, php-performance-analyst, doctrine-migration-reviewer |
| haiku | php-code-explorer, php-test-runner |

Skills:
- `php-conventions`: Stil, Entwurf, Datenbank/Doctrine, Tests und Werkzeuge für modernes PHP 8.
- `php-security-checklist`: allgemeine PHP-Sicherheitscheckliste mit Rückgabeformat.
- `php-design-patterns`: Entscheidungshilfe mit Einsatz, Fallstricken und Symfony-Entsprechungen für 35 Entwurfsmuster. Beim Aufruf etwa 6.000 Tokens.
- `php-delegation-routing`: Routing-Tabelle und Pflicht-Einsatz.

## Aufbau der Konventionen

Jede Regel steht genau einmal, auf der allgemeinsten Ebene, für die sie gilt. Höhere Ebenen laden die tieferen und ergänzen nur.

| Ebene | Plugin | Skill |
|---|---|---|
| Basis (alle Stacks) | developer-workflow-agents | `engineering-conventions` |
| PHP | php-agents | `php-conventions`, `php-security-checklist`, `php-design-patterns` |
| Symfony | symfony-agents | `symfony-conventions`, `symfony-security-checklist` |

Die Agents laden ihren Skill über das Skill-Tool, der Skill lädt die Ebene darunter. Lässt sich ein Skill nicht laden, melden die Agents das in der Rückgabe.

## Quellen des Muster-Skills

Der Skill ist eine eigene Zusammenfassung in eigenen Worten. Texte und Codebeispiele sind nicht übernommen. Die Quellen:

- [Design Patterns PHP](https://designpatternsphp.readthedocs.io/en/latest/) (Repository unter MIT-Lizenz)
- [Refactoring.Guru: Design Patterns in PHP](https://refactoring.guru/design-patterns/php) (urheberrechtlich geschützt, nur verlinkt)

## Empfohlene Ergänzungen im eigenen Projekt

Ein Plugin lädt keine `CLAUDE.md` und keine Berechtigungen ins Projekt. Beides liegt als Vorlage unter `examples/`:

- `examples/CLAUDE.template.md`: Block für die eigene `CLAUDE.md`. Docker-Servicenamen und Testbefehle eintragen.
- `examples/settings.json`: Berechtigungen für `.claude/settings.json`. Erlaubt `docker compose exec`, `composer audit` und lesende `git`-Befehle, sperrt `ssh`/`scp`/`rsync` und das Lesen von `.env*`. `docker compose exec` erlaubt beliebige Befehle im Container und ist für die lokale Entwicklung gedacht.

## Hinweise

- `CLAUDE_CODE_SUBAGENT_MODEL` nicht setzen, sonst überschreibt die Variable die `model:`-Zeilen aller Agents.
- Modell eines Agents ändern: Zeile `model:` im Frontmatter unter `agents/`.
- Fremde Agents vor dem Einsatz lesen und Tool-Rechte prüfen.
- Review-Agents haben `Bash` für `git diff`. "Nur lesend" ist dort eine Anweisung, keine technische Sperre. Wer das hart will, entfernt `Bash` und übergibt den Diff im Prompt.

## Für Maintainer

```bash
claude plugin validate ./
```

`version` in `.claude-plugin/plugin.json` ist gesetzt. Nutzer bleiben auf dieser Version, bis du sie erhöhst. Abhängigkeiten ohne Versionsbereich folgen dem jeweils aktuellen Stand der anderen Plugins. Für feste Versionsbereiche die Releases mit `claude plugin tag --push` taggen und die Bereiche in `dependencies` eintragen.

## Lizenz

MIT, siehe `LICENSE`.
