---
name: php-design-patterns
description: Use when designing or refactoring PHP/Symfony code, choosing between design patterns, reviewing a class structure for over-engineering, or when a pattern name (Factory, Strategy, Decorator, Repository, Singleton, Service Locator and similar) comes up. Decision guide with when-to-use, pitfalls and Symfony equivalents for 35 patterns.
---

# Entwurfsmuster in PHP und Symfony

Eigene Zusammenfassung zum Nachschlagen. Es sind keine Texte oder Codebeispiele aus den Quellen übernommen. Für Details und Beispielcode die Quellen öffnen:

- Sammlung mit PHP-8-Code: https://designpatternsphp.readthedocs.io/en/latest/ (Repository: https://github.com/DesignPatternsPHP/DesignPatternsPHP, MIT)
- Erklärungen zu den klassischen Mustern: https://refactoring.guru/design-patterns/php (urheberrechtlich geschützt, nur verlinken)

## Arbeitsregeln

1. **Erst das Problem, dann das Muster.** Benenne zuerst, was konkret weh tut: ein wachsendes `if/else`, schwer testbarer Code, harte Abhängigkeit, Duplikate. Ohne ein solches Problem kein Muster.
2. **Erst Sprache und Framework prüfen.** Vieles ist schon gelöst: Konstruktor-Injektion und Autowiring (DI-Container), Enums, `readonly`-Klassen und -Eigenschaften, `match`, Attribute, First-Class-Callables, EventDispatcher, Messenger, Workflow-Komponente. Ein eigenes Muster nur, wenn das nicht reicht.
3. **Kleinste Lösung zuerst.** Ein Interface mit einer einzigen Implementierung und ohne absehbare zweite ist selten nötig. Zwei Zeilen Duplikat sind besser als eine falsche Abstraktion.
4. **Muster nicht in den Namen zwingen.** `UserFactoryStrategyManager` hilft keinem. Klassen nach ihrer Aufgabe benennen. Das Muster darf im Namen stehen, wenn es das Verständnis verbessert (z. B. `…Repository`, `…Decorator`).
5. **Bestehende Konventionen im Projekt gehen vor.** Nachbarcode lesen und denselben Stil übernehmen.
6. **Testbarkeit als Prüfstein.** Lässt sich die Abhängigkeit im Test ersetzen? Wenn nicht, ist die Kopplung zu hart.

## Entscheidungshilfe

| Problem | Naheliegendes Muster |
|---|---|
| Objekte brauchen Abhängigkeiten, die austauschbar und testbar sein sollen | Dependency Injection |
| Verhalten (Algorithmus) soll zur Laufzeit wählbar sein | Strategy |
| Verhalten soll um Zusatzfunktion erweitert werden, ohne die Klasse zu ändern | Decorator |
| Fremde Schnittstelle passt nicht zur eigenen | Adapter |
| Komplexes Subsystem braucht einen einfachen Einstieg | Facade |
| Auf Ereignisse reagieren, ohne den Auslöser zu koppeln | Observer (EventDispatcher) |
| Anfrage als Objekt: einreihen, protokollieren, wiederholen | Command (Messenger) |
| Objekt mit vielen Optionen schrittweise aufbauen | Builder |
| Wahl der konkreten Klasse nach Typ oder Konfiguration | Simple Factory oder Factory Method |
| Zustandsabhängiges Verhalten, viele Statusprüfungen | State (Workflow-Komponente) |
| Fachregeln kombinierbar und einzeln testbar machen | Specification |
| Zugriff auf Domänenobjekte ohne Persistenz-Details | Repository |
| Kein `null` mehr prüfen müssen | Null Object |
| Baumstruktur einheitlich behandeln | Composite |
| Gleichen Ablauf mit variablen Schritten wiederverwenden | Template Method |
| Verarbeitung in Stufen, jede Stufe darf abbrechen | Chain of Responsibility |

## Erzeugungsmuster

- **Abstract Factory** – erzeugt zusammengehörige Objekte einer "Familie", ohne dass der Aufrufer die konkreten Klassen kennt. Nutzen, wenn mehrere Varianten konsistent zusammenpassen müssen (z. B. Zahlungsanbieter mit passendem Client und Mapper). Vorsicht: viele Klassen, nur bei echten Familien.
- **Builder** – setzt ein Objekt mit vielen Teilen oder Optionen Schritt für Schritt zusammen. Nutzen, wenn ein Konstruktor mit vielen optionalen Parametern unlesbar wird. Vorsicht: bei wenigen Parametern genügen benannte Argumente von PHP 8.
- **Factory Method** – eine überschreibbare Methode entscheidet, welche konkrete Klasse entsteht. Nutzen, wenn Unterklassen den Typ bestimmen sollen. Vorsicht: bindet an Vererbung, oft ist Komposition (Simple Factory) einfacher.
- **Simple Factory** – eine Fabrikklasse mit Instanzmethode erzeugt Objekte. Gut testbar, mehrere konfigurierte Fabriken möglich, per DI austauschbar. Meist die bessere Wahl gegenüber der Static Factory.
- **Static Factory** – statische Methode, die passende Objekte liefert. Vorsicht: statischer Aufruf ist globale Kopplung, schwer zu mocken, nicht austauschbar. Eher für kleine Wertobjekte (benannte Konstruktoren wie `fromString()`), nicht für Services.
- **Prototype** – neue Objekte entstehen durch Kopieren einer Vorlage. Nutzen, wenn das Erzeugen teuer ist und Kopien nur leicht abweichen. Vorsicht: Tiefe Kopie und `__clone` sauber umsetzen.
- **Object Pool** – hält vorbereitete Objekte zur Wiederverwendung bereit. Lohnt sich bei teuren Ressourcen wie Verbindungen. Vorsicht: für leichte Objekte bremst es eher. In PHP-Requests selten sinnvoll, in langlaufenden Workern (Messenger, Swoole) eher.
- **Singleton** – genau eine Instanz mit globalem Zugriff. Gilt allgemein als Anti-Pattern: versteckte Abhängigkeit, globaler Zustand, schlecht testbar. Stattdessen den DI-Container nutzen: Services sind dort standardmäßig geteilt (eine Instanz pro Container).

## Strukturmuster

- **Adapter** – übersetzt eine Schnittstelle in die erwartete. Nutzen für Fremdbibliotheken und SDKs: hinter eigener Schnittstelle kapseln, damit sich der Anbieter tauschen lässt.
- **Bridge** – trennt Abstraktion und Implementierung in zwei Hierarchien, die unabhängig wachsen. Nutzen, wenn sonst Kombinationen explodieren (z. B. Nachrichtenart × Versandkanal). Vorsicht: bei zwei Varianten übertrieben.
- **Composite** – Einzelobjekte und Gruppen teilen dieselbe Schnittstelle, so entsteht eine Baumstruktur (Menüs, Kategorien, Formulare). Vorsicht: Schnittstelle nicht mit Methoden aufblasen, die nur für Blätter oder nur für Knoten sinnvoll sind.
- **Data Mapper** – überträgt Daten zwischen Datenbank und Domänenobjekten, beide kennen einander nicht. Das Domänenobjekt bleibt frei von Persistenz. Doctrine ORM arbeitet nach diesem Prinzip. Gegenpol: Active Record.
- **Decorator** – umhüllt ein Objekt gleicher Schnittstelle und ergänzt Verhalten (Caching, Logging, Berechtigung). In Symfony: Service-Decoration (`#[AsDecorator]`). Vorsicht: lange Ketten sind schwer zu debuggen, Reihenfolge ist wichtig.
- **Dependency Injection** – Abhängigkeiten kommen von außen, meist über den Konstruktor. Ergebnis: lose Kopplung, Austauschbarkeit, Testbarkeit. Standard in Symfony über Autowiring. Konstruktor-Injektion vor Setter-Injektion bevorzugen.
- **Facade** – einfache Schnittstelle vor einem komplexen Subsystem. Nutzen für Anwendungsdienste, die mehrere Services koordinieren. Vorsicht: nicht zur "Gott-Klasse" werden lassen.
- **Fluent Interface** – Methodenaufrufe werden verkettet, jede liefert das Objekt zurück. Lesbar für Builder und Query-Aufbau. Vorsicht: Bei veränderlichen Objekten sind Nebenwirkungen unsichtbar, bei Wertobjekten besser `with…()` mit neuer Instanz.
- **Flyweight** – teilt gemeinsamen, unveränderlichen Zustand zwischen vielen Objekten, um Speicher zu sparen. Nur bei sehr vielen gleichartigen Objekten und gemessenem Speicherproblem.
- **Proxy** – Stellvertreter mit derselben Schnittstelle, steuert den Zugriff (Lazy Loading, Zugriffsschutz, Caching). Doctrine nutzt Proxies für nachgeladene Entities, Symfony kennt Lazy Services. Vorsicht: Klassen sollten nicht `final` sein, wo Proxies sie erweitern müssen.
- **Registry** – zentraler, global erreichbarer Speicher für Objekte. Erzeugt globalen Zustand und ist schwer zu mocken. Stattdessen DI.

## Verhaltensmuster

- **Chain of Responsibility** – eine Anfrage wandert durch Handler, einer bearbeitet sie oder reicht sie weiter. Beispiele: Messenger-Middleware, Validierungs- oder Berechtigungsstufen. Vorsicht: klar festlegen, was passiert, wenn niemand zuständig ist.
- **Command** – verpackt eine Anfrage als Objekt, das sich übergeben, einreihen, protokollieren und wiederholen lässt. In Symfony: Messenger-Nachrichten mit Handler. Nachrichten unveränderlich und serialisierbar halten.
- **Interpreter** – bildet die Regeln einer kleinen Sprache als Klassen ab und wertet Ausdrücke aus. Selten nötig. Für Ausdrücke vorher prüfen, ob die Symfony-Komponente ExpressionLanguage reicht.
- **Iterator** – durchläuft eine Sammlung, ohne ihre Struktur offenzulegen. In PHP über `Iterator`, `IteratorAggregate` und Generatoren (`yield`). Generatoren sind für große Datenmengen speicherschonend.
- **Mediator** – Objekte sprechen über eine Vermittlerinstanz statt direkt miteinander. Reduziert Abhängigkeiten. Vorsicht: Der Vermittler kann selbst zum Monolithen werden. Der EventDispatcher erfüllt eine ähnliche Rolle.
- **Memento** – speichert den Zustand eines Objekts, um ihn später wiederherzustellen (Undo, Entwürfe). Der Schnappschuss bleibt unveränderlich und für Außenstehende undurchsichtig.
- **Null Object** – ein Objekt, das nichts tut, ersetzt `null`. Der Aufrufer braucht keine Prüfung mehr. Beispiel: ein Logger, der nichts ausgibt (PSR-3 `NullLogger`). Kein GoF-Muster, aber verbreitet. Vorsicht: Nicht dort einsetzen, wo ein fehlender Wert ein echter Fehler wäre.
- **Observer** – Beobachter melden sich an und werden bei Änderungen benachrichtigt. In Symfony: EventDispatcher mit Listenern und Subscribern. Vorsicht: Ablauf wird indirekt, Reihenfolge und Seiteneffekte dokumentieren, Listener schlank halten.
- **Specification** – eine Fachregel als eigenes Objekt, das eine Ja/Nein-Frage beantwortet. Regeln lassen sich mit Und, Oder, Nicht kombinieren, ohne das geprüfte Objekt zu ändern. Gut für wiederverwendbare Geschäftsregeln. Vorsicht: bei einer einzelnen Regel reicht eine Methode.
- **State** – Verhalten hängt vom inneren Zustand ab, jeder Zustand ist eine eigene Klasse. Ersetzt große `switch`-Blöcke auf einem Statusfeld. In Symfony bildet die Workflow-Komponente Zustände und erlaubte Übergänge ab. Bei einfachen Statuswerten reicht ein Enum.
- **Strategy** – austauschbare Algorithmen hinter einer Schnittstelle. Ersetzt `if/else` auf einem Typ. In Symfony: markierte Services (Tags) per `tagged_iterator` sammeln. Bei einer reinen Funktion genügt oft ein Callable.
- **Template Method** – Basisklasse legt den Ablauf fest, Unterklassen füllen einzelne Schritte. Vorsicht: Vererbung koppelt stark. Wenn die Schritte wechseln sollen, ist Strategy flexibler.
- **Visitor** – trennt einen Algorithmus von den Objektstrukturen, auf denen er arbeitet. Nutzen, wenn viele verschiedene Operationen auf einer stabilen Klassenstruktur nötig sind. Vorsicht: Jede neue Elementklasse erzwingt Änderungen an allen Visitors.

## Weitere Muster

- **Service Locator** – ein Objekt liefert Services auf Anfrage. Viele sehen darin ein Anti-Pattern, weil Abhängigkeiten versteckt bleiben und sich nicht im Konstruktor ablesen lassen. Den Container nicht in Klassen injizieren und von dort abfragen. Ausnahme: ein gezielt eingegrenzter, typisierter Locator (Symfony `ServiceLocator` über Tags) für wenige zur Laufzeit gewählte Services.
- **Repository** – vermittelt zwischen Domäne und Datenzugriff, bietet Zugriff wie auf eine Sammlung von Domänenobjekten. Die Domäne kennt nur die Schnittstelle, nicht die Persistenz. Suchlogik gehört ins Repository, nicht in Controller und Services. Doctrine liefert Repository-Klassen mit.
- **Entity-Attribute-Value (EAV)** – speichert Eigenschaften als Name-Wert-Paare statt als feste Spalten. Nur bei sehr vielen möglichen, selten belegten Attributen. Vorsicht: schwer abzufragen und zu validieren, Abfragen werden langsam. Erst prüfen, ob JSON-Spalten oder getrennte Tabellen reichen.

## Typische Fehlgriffe, auf die du achten sollst

- Singleton, Registry und Service Locator als Abkürzung für fehlende Konstruktor-Injektion.
- Statische Methoden mit Zustand oder Seiteneffekten.
- Interfaces ohne zweite Implementierung und ohne Testbedarf.
- Vererbungsketten, wo Komposition einfacher wäre (Template Method, Factory Method).
- Muster als Selbstzweck: mehr Klassen, aber kein gelöstes Problem.
- Fachlogik in Controllern oder Listenern statt in Domänen- oder Anwendungsdiensten.
- Fachliche Prüfungen verstreut statt gebündelt (Specification oder eigene Methode am Objekt).

## Vorgehen bei Entwurf und Review

1. Problem in einem Satz formulieren.
2. Prüfen, ob PHP 8 oder Symfony es schon lösen.
3. Falls nicht: die einfachste passende Lösung wählen, das Muster optional benennen.
4. Im Review nach diesen Fragen prüfen: Welches Problem löst die Abstraktion? Gibt es dafür einen Test oder einen zweiten Anwendungsfall? Bleibt der Code ohne das Muster einfacher?
