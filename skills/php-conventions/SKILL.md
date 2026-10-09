---
name: php-conventions
description: Use when writing, changing, testing or reviewing PHP code in any framework. Coding, design, database and test conventions for modern PHP 8, built on the shared engineering conventions.
---

# PHP conventions

First load `developer-workflow-agents:engineering-conventions` through the Skill tool, if not done yet. The rules there still apply. This skill only adds what is specific to PHP.

The project's configuration and neighboring code take precedence (code style configuration, `phpstan.neon`). The PHP version is in `composer.json` (`require.php`). Do not use language features above it.

## Language and style

- `declare(strict_types=1);` in every file.
- Types everywhere: parameters, return values, properties. Avoid `mixed`. Docblock types only where the language cannot express them (e.g. `array<int, Foo>`).
- Value objects immutable: `readonly` properties (from 8.1), `readonly` classes (from 8.2).
- Enums instead of string or int constants for fixed sets of values (from 8.1). `match` instead of long `if`/`switch` chains when every branch returns a value.
- Use constructor promotion, named arguments when there are many parameters.
- Strict comparisons (`===`), `==` only with a reason. Compare secrets with `hash_equals`.
- Style according to PER Coding Style (successor of PSR-12), autoloading according to PSR-4.
- Import every class with `use`, classes from the global namespace included: `use DateTimeInterface;` and then `?DateTimeInterface`, never `?\DateTimeInterface`. This covers type hints, `new`, `instanceof`, static calls and class constants. Not covered: class names inside strings and service ids in configuration.

## Design

- Inject dependencies through the constructor. No `new` for services, no static calls with state, no global state.
- Small classes with one job. Business logic belongs in domain or application services, not in controllers, console commands or listeners.
- No public properties. Every property is private, protected only where a subclass needs it, promoted constructor properties, entities and value objects included. Access from outside through a getter, and a getter only when there is a legitimate interest in the property. A property passed in as a constructor argument has that interest. Not covered: properties of test doubles declared inside a test file.
- Own, meaningful exceptions instead of generic ones. Do not swallow errors, do not use exceptions for normal control flow. An empty `catch` block always carries a comment stating why swallowing the exception is intended.
- Use `null` deliberately: return type `?Foo` only when "not present" is a normal case, otherwise an exception.
- Design patterns only for a concrete problem. Reference: skill `php-agents:php-design-patterns`.

## Database and Doctrine

- Access through repositories, no scattered queries. Bind parameters, never concatenate values into SQL/DQL.
- No queries in loops (N+1): load relations with a join and `addSelect`, or query in batches.
- Multi-part writes in one transaction. `flush()` in bulk, not per object in a loop.
- Process large sets in batches and free memory with `clear()`.
- Schema changes only through migrations. Have every migration reviewed before it runs (agent `php-agents:doctrine-migration-reviewer`). Entity mapping and migration must match.

## Tests and tooling

- Take the commands from `composer.json` (`scripts`) or the `Makefile`. Static analysis (PHPStan/Psalm) and code style (ECS/PHP-CS-Fixer) with the project's configuration.
- PHPUnit: Arrange-Act-Assert, one behavior per test, meaningful test names. Mocks only at the boundaries (I/O, time, network), do not mock your own value objects.
- Unit tests without a database. Database access in integration tests against the test database.
- Cover edge cases: empty values, boundary values, error paths.
- If you find a bug in production code while testing, report it and do not fix it unasked.
