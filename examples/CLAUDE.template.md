# Template for the project CLAUDE.md

A plugin cannot ship a `CLAUDE.md` that is loaded automatically. Copy this block into your own `CLAUDE.md`. It only points to the skills and does not repeat any rules, so they are maintained in one place.

---

## PHP

- Conventions: skill `php-agents:php-conventions` (pulls in `developer-workflow-agents:engineering-conventions`).
- Delegation and mandatory use: skill `php-agents:php-delegation-routing`.
- Before every commit: `php-agents:php-code-reviewer`. For every migration: `php-agents:doctrine-migration-reviewer`. For payment, login and permissions: `php-agents:php-security-auditor`.

## Project-specific (fill in)

- PHP version: `...`
- Docker service name: `...`
- Test command, e.g. `docker compose exec <service> vendor/bin/phpunit`: `...`
- Static analysis and code style: `...`
