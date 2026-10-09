---
name: php-delegation-routing
description: Use when delegating PHP work (code search, tests, implementation, review, Doctrine migrations, security, performance) to subagents, or when choosing which PHP agent fits a task. Contains the routing table and the mandatory reviews.
---

# Delegation (PHP)

Delegiere nach Entscheidungskomplexität, nicht nach Aufgabenart. Das Modell steht im Frontmatter des jeweiligen Agents. Konventionen: Skill `php-agents:php-conventions`.

| Aufgabe | Agent |
|---|---|
| Dateien, Symbole, Aufrufstellen finden | php-code-explorer |
| Tests, PHPStan, ECS ausführen | php-test-runner |
| Aufgabe mit klarem Plan umsetzen | php-implementer |
| Tests schreiben | php-test-writer |
| Diff prüfen | php-code-reviewer |
| Doctrine-Migration oder Schema prüfen | doctrine-migration-reviewer |
| Langsame Pfade finden | php-performance-analyst |
| Sicherheitsprüfung | php-security-auditor |

In Symfony-Projekten ersetzt das Plugin `symfony-agents` die Agents für Umsetzung, Tests, Review und Sicherheit durch seine `symfony-*`-Varianten. Die übrigen Agents bleiben.

## Pflicht-Einsatz

- Vor jedem Commit: Review-Agent.
- Bei jeder Migration oder Schema-Änderung: doctrine-migration-reviewer. Migrationen nie ohne Prüfung ausführen.
- Bei Änderungen an Zahlung, Login oder Berechtigungen: Security-Auditor.

Planung, Issues und PRs übernimmt `developer-workflow-agents`, falls installiert.
