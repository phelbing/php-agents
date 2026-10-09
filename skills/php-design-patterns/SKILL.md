---
name: php-design-patterns
description: Use when designing or refactoring PHP/Symfony code, choosing between design patterns, reviewing a class structure for over-engineering, or when a pattern name (Factory, Strategy, Decorator, Repository, Singleton, Service Locator and similar) comes up. Decision guide with when-to-use, pitfalls and Symfony equivalents for 35 patterns.
---

# Design patterns in PHP and Symfony

Own summary for reference. No texts or code examples are taken from the sources. For details and example code, open the sources:

- Collection with PHP 8 code: https://designpatternsphp.readthedocs.io/en/latest/ (repository: https://github.com/DesignPatternsPHP/DesignPatternsPHP, MIT)
- Explanations of the classic patterns: https://refactoring.guru/design-patterns/php (copyrighted, link only)

## Working rules

1. **Problem first, then the pattern.** First name what concretely hurts: a growing `if/else`, code that is hard to test, a hard dependency, duplication. No such problem, no pattern.
2. **Check the language and the framework first.** Much is already solved: constructor injection and autowiring (DI container), enums, `readonly` classes and properties, `match`, attributes, first-class callables, EventDispatcher, Messenger, Workflow component. Write your own pattern only when that is not enough.
3. **Smallest solution first.** An interface with a single implementation and no second one in sight is rarely needed. Two lines of duplication are better than a wrong abstraction.
4. **Do not force patterns into names.** `UserFactoryStrategyManager` helps nobody. Name classes after their job. The pattern may appear in the name when it helps understanding (e.g. `…Repository`, `…Decorator`).
5. **Existing project conventions take precedence.** Read the neighboring code and follow its style.
6. **Testability as the touchstone.** Can the dependency be replaced in a test? If not, the coupling is too tight.

## Decision guide

| Problem | Obvious pattern |
|---|---|
| Objects need dependencies that should be replaceable and testable | Dependency Injection |
| Behavior (algorithm) should be selectable at runtime | Strategy |
| Behavior should be extended without changing the class | Decorator |
| A third-party interface does not fit your own | Adapter |
| A complex subsystem needs a simple entry point | Facade |
| React to events without coupling to the trigger | Observer (EventDispatcher) |
| Request as an object: queue, log, retry | Command (Messenger) |
| Build an object with many options step by step | Builder |
| Choose the concrete class by type or configuration | Simple Factory or Factory Method |
| State-dependent behavior, many status checks | State (Workflow component) |
| Make business rules combinable and testable on their own | Specification |
| Access domain objects without persistence details | Repository |
| No more `null` checks | Null Object |
| Treat a tree structure uniformly | Composite |
| Reuse the same flow with variable steps | Template Method |
| Processing in stages, each stage may abort | Chain of Responsibility |

## Creational patterns

- **Abstract Factory** – creates related objects of a "family" without the caller knowing the concrete classes. Use when several variants must fit together consistently (e.g. a payment provider with a matching client and mapper). Watch out: many classes, only for real families.
- **Builder** – assembles an object with many parts or options step by step. Use when a constructor with many optional parameters becomes unreadable. Watch out: with few parameters, PHP 8 named arguments are enough.
- **Factory Method** – an overridable method decides which concrete class is created. Use when subclasses should determine the type. Watch out: ties you to inheritance, composition (Simple Factory) is often simpler.
- **Simple Factory** – a factory class with an instance method creates objects. Easy to test, several configured factories possible, replaceable via DI. Usually the better choice over the Static Factory.
- **Static Factory** – a static method that returns suitable objects. Watch out: a static call is global coupling, hard to mock, not replaceable. Rather for small value objects (named constructors such as `fromString()`), not for services.
- **Prototype** – new objects are created by copying a template. Use when creation is expensive and copies differ only slightly. Watch out: implement deep copies and `__clone` properly.
- **Object Pool** – keeps prepared objects ready for reuse. Pays off for expensive resources such as connections. Watch out: for lightweight objects it tends to slow things down. Rarely useful in PHP requests, more so in long-running workers (Messenger, Swoole).
- **Singleton** – exactly one instance with global access. Generally considered an anti-pattern: hidden dependency, global state, hard to test. Use the DI container instead: services are shared there by default (one instance per container).

## Structural patterns

- **Adapter** – translates one interface into the expected one. Use for third-party libraries and SDKs: wrap them behind your own interface so the provider can be swapped.
- **Bridge** – separates abstraction and implementation into two hierarchies that grow independently. Use when combinations would otherwise explode (e.g. message type × delivery channel). Watch out: overkill for two variants.
- **Composite** – single objects and groups share the same interface, forming a tree structure (menus, categories, forms). Watch out: do not bloat the interface with methods that only make sense for leaves or only for nodes.
- **Data Mapper** – moves data between the database and domain objects, neither knows the other. The domain object stays free of persistence. Doctrine ORM works on this principle. Counterpart: Active Record.
- **Decorator** – wraps an object with the same interface and adds behavior (caching, logging, authorization). In Symfony: service decoration (`#[AsDecorator]`). Watch out: long chains are hard to debug, order matters.
- **Dependency Injection** – dependencies come from outside, usually through the constructor. Result: loose coupling, replaceability, testability. Standard in Symfony via autowiring. Prefer constructor injection over setter injection.
- **Facade** – a simple interface in front of a complex subsystem. Use for application services that coordinate several services. Watch out: do not let it become a "god class".
- **Fluent Interface** – method calls are chained, each returns the object. Readable for builders and query construction. Watch out: with mutable objects side effects are invisible, for value objects prefer `with…()` returning a new instance.
- **Flyweight** – shares common, immutable state between many objects to save memory. Only with very many similar objects and a measured memory problem.
- **Proxy** – a stand-in with the same interface that controls access (lazy loading, access protection, caching). Doctrine uses proxies for lazily loaded entities, Symfony has lazy services. Watch out: classes should not be `final` where proxies need to extend them.
- **Registry** – a central, globally reachable store for objects. Creates global state and is hard to mock. Use DI instead.

## Behavioral patterns

- **Chain of Responsibility** – a request passes through handlers, one handles it or passes it on. Examples: Messenger middleware, validation or authorization stages. Watch out: define clearly what happens when nobody is responsible.
- **Command** – wraps a request as an object that can be passed on, queued, logged and retried. In Symfony: Messenger messages with a handler. Keep messages immutable and serializable.
- **Interpreter** – models the rules of a small language as classes and evaluates expressions. Rarely needed. For expressions, check first whether the Symfony ExpressionLanguage component is enough.
- **Iterator** – traverses a collection without exposing its structure. In PHP via `Iterator`, `IteratorAggregate` and generators (`yield`). Generators save memory for large data sets.
- **Mediator** – objects talk through a mediator instead of directly with each other. Reduces dependencies. Watch out: the mediator can become a monolith itself. The EventDispatcher plays a similar role.
- **Memento** – stores an object's state to restore it later (undo, drafts). The snapshot stays immutable and opaque to outsiders.
- **Null Object** – an object that does nothing replaces `null`. The caller no longer needs a check. Example: a logger that outputs nothing (PSR-3 `NullLogger`). Not a GoF pattern, but common. Watch out: do not use it where a missing value would be a real error.
- **Observer** – observers subscribe and are notified of changes. In Symfony: EventDispatcher with listeners and subscribers. Watch out: the flow becomes indirect, document order and side effects, keep listeners lean.
- **Specification** – a business rule as its own object that answers a yes/no question. Rules can be combined with and, or, not, without changing the checked object. Good for reusable business rules. Watch out: for a single rule, a method is enough.
- **State** – behavior depends on the internal state, each state is its own class. Replaces large `switch` blocks on a status field. In Symfony, the Workflow component models states and allowed transitions. For simple status values an enum is enough.
- **Strategy** – interchangeable algorithms behind one interface. Replaces `if/else` on a type. In Symfony: collect tagged services via `tagged_iterator`. For a pure function, a callable is often enough.
- **Template Method** – a base class defines the flow, subclasses fill in individual steps. Watch out: inheritance couples tightly. When the steps should vary, Strategy is more flexible.
- **Visitor** – separates an algorithm from the object structures it works on. Use when many different operations are needed on a stable class structure. Watch out: every new element class forces changes to all visitors.

## Other patterns

- **Service Locator** – an object returns services on request. Many consider it an anti-pattern because dependencies stay hidden and cannot be read from the constructor. Do not inject the container into classes and fetch services from it. Exception: a deliberately narrow, typed locator (Symfony `ServiceLocator` via tags) for a few services chosen at runtime.
- **Repository** – mediates between the domain and data access, offers access like a collection of domain objects. The domain only knows the interface, not the persistence. Query logic belongs in the repository, not in controllers and services. Doctrine ships repository classes.
- **Entity-Attribute-Value (EAV)** – stores properties as name-value pairs instead of fixed columns. Only for very many possible, rarely filled attributes. Watch out: hard to query and validate, queries get slow. First check whether JSON columns or separate tables are enough.

## Typical mistakes to watch for

- Singleton, Registry and Service Locator as a shortcut for missing constructor injection.
- Static methods with state or side effects.
- Interfaces without a second implementation and without a testing need.
- Inheritance chains where composition would be simpler (Template Method, Factory Method).
- Patterns for their own sake: more classes, but no solved problem.
- Business logic in controllers or listeners instead of domain or application services.
- Business checks scattered instead of bundled (Specification or a method on the object).

## Approach for design and review

1. State the problem in one sentence.
2. Check whether PHP 8 or Symfony already solves it.
3. If not: choose the simplest fitting solution, optionally name the pattern.
4. In a review, check with these questions: Which problem does the abstraction solve? Is there a test or a second use case for it? Would the code be simpler without the pattern?
