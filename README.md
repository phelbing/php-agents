# php-agents

8 subagents with model routing (haiku, sonnet, opus) for PHP and Doctrine: code search, tests, review, migrations, security and performance. Plus PHP conventions and a skill with design patterns. Works with any PHP framework. For Symfony projects, `symfony-agents` adds to it (separate repo).

## Installation

`php-agents` builds on `developer-workflow-agents`. The marketplaces of the dependencies must be added first, otherwise the plugin does not load:

```bash
claude plugin marketplace add phelbing/developer-workflow-agents
claude plugin marketplace add phelbing/php-agents
claude plugin install php-agents@php-agents
```

The dependency `developer-workflow-agents` is installed automatically. Then run `/agents` in Claude Code. The agents carry the plugin prefix, e.g. `php-agents:php-code-reviewer`.

## Contents

| Model | Agents |
|---|---|
| opus | php-security-auditor, doctrine-migration-reviewer |
| sonnet | php-implementer, php-test-writer, php-code-reviewer, php-performance-analyst |
| haiku | php-code-explorer, php-test-runner |

Skills:
- `php-conventions`: style, design, database/Doctrine, tests and tooling for modern PHP 8.
- `php-security-checklist`: generic PHP security checklist with report format.
- `php-design-patterns`: decision guide with use cases, pitfalls and Symfony equivalents for 35 design patterns.
- `php-delegation-routing`: routing table and mandatory use.

## Structure of the conventions

Every rule exists exactly once, on the most general level it applies to. Higher levels load the lower ones and only add to them.

| Level | Plugin | Skill |
|---|---|---|
| Base (all stacks) | developer-workflow-agents | `engineering-conventions` |
| PHP | php-agents | `php-conventions`, `php-security-checklist`, `php-design-patterns` |
| Symfony | symfony-agents | `symfony-conventions`, `symfony-security-checklist` |

The agents load their skills at startup through the `skills` field in the frontmatter, including all levels below. In the main conversation, a skill loads the level below through the Skill tool. If a skill cannot be loaded, the agents say so in their report.

## Sources of the patterns skill

The skill is an own summary in own words. No texts or code examples are taken over. The sources:

- [Design Patterns PHP](https://designpatternsphp.readthedocs.io/en/latest/) (repository under the MIT license)
- [Refactoring.Guru: Design Patterns in PHP](https://refactoring.guru/design-patterns/php) (copyrighted, linked only)

## Recommended additions to your project

A plugin does not load a `CLAUDE.md` or permissions into the project. Both are provided as templates under `examples/`:

- `examples/CLAUDE.template.md`: block for your own `CLAUDE.md`. Fill in the Docker service names and test commands.
- `examples/settings.json`: permissions for `.claude/settings.json`. Allows `docker compose exec`, `composer audit` and read-only `git` commands, blocks `docker compose down -v`, `ssh`/`scp`/`rsync` and reading `.env*`. `docker compose exec` allows any command in the container and is meant for local development.

## Notes

- Do not set `CLAUDE_CODE_SUBAGENT_MODEL`, otherwise the variable overrides the `model:` lines of all agents.
- To change an agent's model: the `model:` line in the frontmatter under `agents/`.
- Read third-party agents before using them and check their tool permissions.
- Review agents have `Bash` for `git diff`. "Read-only" is an instruction there, not a technical block. If you want it enforced, remove `Bash` and pass the diff in the prompt.
- The `deny` rules in `examples/settings.json` match the command as written. `rm -fr` or `/bin/rm -rf` are not covered. The rules guard against mistakes, they are not a hard block.

## For maintainers

```bash
claude plugin validate ./
```

`version` in `.claude-plugin/plugin.json` is set. Users stay on this version until you raise it. Dependencies without a version range follow the current state of the other plugins. For fixed version ranges, tag the releases with `claude plugin tag --push` and add the ranges to `dependencies`.

## License

MIT, see `LICENSE`.
