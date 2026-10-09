---
name: php-delegation-routing
description: Use when delegating PHP work (code search, tests, implementation, review, Doctrine migrations, security, performance) to subagents, or when choosing which PHP agent fits a task. Contains the routing table and the mandatory reviews.
---

# Delegation (PHP)

Delegate by decision complexity, not by type of task. The model is set in the frontmatter of each agent. Conventions: skill `php-agents:php-conventions`.

Call agents with the plugin prefix, e.g. `php-agents:php-code-reviewer`.

## 1. Routing

| Task | Agent |
|---|---|
| Find files, symbols, call sites | php-code-explorer |
| Run tests, PHPStan, ECS | php-test-runner |
| Implement a task with a clear plan | php-implementer |
| Write tests | php-test-writer |
| Review a diff | php-code-reviewer |
| Review a Doctrine migration or schema | doctrine-migration-reviewer |
| Find slow paths | php-performance-analyst |
| Security review | php-security-auditor |

In Symfony projects, the `symfony-agents` plugin replaces the agents for implementation, tests, review and security with its `symfony-*` variants. The other agents stay.

## 2. Mandatory use

1. Before every commit: review agent.
2. For every migration or schema change: doctrine-migration-reviewer. Never run migrations without a review.
3. For changes to payment, login or permissions: security auditor.

Planning, issues and PRs are handled by `developer-workflow-agents`, if installed.
