# Architecture

## 1. Structure of the conventions

Every rule exists exactly once, on the most general level it applies to. Each level loads the level it builds on and only adds to it. The full table of all levels is in [developer-workflow-agents/docs/architecture.md](https://github.com/phelbing/developer-workflow-agents/blob/main/docs/architecture.md).

| Level | Plugin | Builds on | Skills |
|---|---|---|---|
| PHP | php-agents | developer-workflow-agents | `php-conventions`, `php-security-checklist`, `php-design-patterns`, `php-delegation-routing` |

The agents load their skills at startup through the `skills` field in the frontmatter, including every level they build on. In the main conversation, a conventions or checklist skill loads the level it builds on through the Skill tool. The delegation routing skills stand alone and do not load each other. If a skill cannot be loaded, the agents say so in their report.
