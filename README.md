# php-agents

8 subagents with model routing (haiku, sonnet, opus) for PHP and Doctrine: code search, tests, review, migrations, security and performance. Plus PHP conventions and a skill with design patterns. Works with any PHP framework. For Symfony projects, `symfony-agents` adds to it (separate repo).

## 1. Installation

`php-agents` builds on `developer-workflow-agents`. The marketplaces of the dependencies must be added first, otherwise the plugin does not load:

```bash
claude plugin marketplace add phelbing/developer-workflow-agents
claude plugin marketplace add phelbing/php-agents
claude plugin install php-agents@php-agents
```

The dependency `developer-workflow-agents` is installed automatically. Then run `/agents` in Claude Code. The agents carry the plugin prefix, e.g. `php-agents:php-code-reviewer`.

## 2. Contents

| Model | Agents |
|---|---|
| opus | php-security-auditor, doctrine-migration-reviewer |
| sonnet | php-implementer, php-test-writer, php-code-reviewer, php-performance-analyst |
| haiku | php-code-explorer, php-test-runner |

Skills:
1. `php-conventions`: style, design, database/Doctrine, tests and tooling for modern PHP 8.
2. `php-security-checklist`: generic PHP security checklist with report format.
3. `php-design-patterns`: decision guide with use cases, pitfalls and Symfony equivalents for 35 design patterns.
4. `php-delegation-routing`: routing table and mandatory use.

## 3. Recommended additions to your project

A plugin does not load a `CLAUDE.md` or permissions into the project. Both are provided as templates under `examples/`:

1. `examples/CLAUDE.template.md`: block for your own `CLAUDE.md`. Fill in the Docker service names and test commands.
2. `examples/settings.json`: permissions for `.claude/settings.json`. Allows `docker compose exec`, `composer audit` and read-only `git` commands, blocks `docker compose down -v`, `ssh`/`scp`/`rsync` and reading `.env*`. `docker compose exec` allows any command in the container and is meant for local development.

## 4. Notes

1. Do not set `CLAUDE_CODE_SUBAGENT_MODEL`, otherwise the variable overrides the `model:` lines of all agents.
2. Read third-party agents before using them and check their tool permissions.
3. Review agents have `Bash` for `git diff`. "Read-only" is an instruction there, not a technical block. If you want it enforced, remove `Bash` and pass the diff in the prompt.
4. The `deny` rules in `examples/settings.json` match the command as written. `rm -fr` or `/bin/rm -rf` are not covered. The rules guard against mistakes, they are not a hard block.

## 5. Documentation

1. [docs/architecture.md](docs/architecture.md): how the conventions are layered and how the agents load their skills.
2. [docs/maintenance.md](docs/maintenance.md): validation, agent models and versions, for maintainers.

## 6. License

MIT, see `LICENSE`.
