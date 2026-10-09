# Vorlage für die CLAUDE.md im Projekt

Ein Plugin kann keine `CLAUDE.md` mitliefern, die automatisch geladen wird. Diesen Block in die eigene `CLAUDE.md` übernehmen. Er verweist nur auf die Skills und wiederholt keine Regeln, damit sie nur an einer Stelle gepflegt werden.

---

## PHP

- Konventionen: Skill `php-agents:php-conventions` (zieht `developer-workflow-agents:engineering-conventions` mit).
- Delegation und Pflicht-Einsatz: Skill `php-agents:php-delegation-routing`.
- Vor jedem Commit: `php-code-reviewer`. Bei jeder Migration: `doctrine-migration-reviewer`. Bei Zahlung, Login und Berechtigungen: `php-security-auditor`.

## Projektspezifisch (hier eintragen)

- PHP-Version: `...`
- Docker-Servicename: `...`
- Testbefehl, z. B. `docker compose exec <service> vendor/bin/phpunit`: `...`
- Static Analysis und Code-Style: `...`
