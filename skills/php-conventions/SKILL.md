---
name: php-conventions
description: Use when writing, changing, testing or reviewing PHP code in any framework. Coding, design, database and test conventions for modern PHP 8, built on the shared engineering conventions.
---

# PHP-Konventionen

Lade zuerst per Skill-Tool `developer-workflow-agents:engineering-conventions`, falls noch nicht geschehen. Die Regeln dort gelten weiter. Dieser Skill ergänzt nur, was PHP-spezifisch ist.

Konfiguration und Nachbarcode des Projekts gehen vor (Code-Style-Konfiguration, `phpstan.neon`). Die PHP-Version steht in `composer.json` (`require.php`). Keine Sprachfeatures nutzen, die darüber liegen.

## Sprache und Stil

- `declare(strict_types=1);` in jeder Datei.
- Typen überall: Parameter, Rückgaben, Properties. `mixed` vermeiden. Docblock-Typen nur dort, wo die Sprache sie nicht ausdrücken kann (z. B. `array<int, Foo>`).
- Wertobjekte unveränderlich: `readonly`-Properties (ab 8.1), `readonly`-Klassen (ab 8.2).
- Enums statt String- oder Int-Konstanten für feste Wertemengen (ab 8.1). `match` statt langer `if`/`switch`-Ketten, wenn jeder Zweig einen Wert liefert.
- Constructor Promotion nutzen, bei vielen Parametern benannte Argumente.
- Strikte Vergleiche (`===`), `==` nur mit Begründung. Geheimnisse mit `hash_equals` vergleichen.
- Stil nach PER Coding Style (Nachfolger von PSR-12), Autoloading nach PSR-4.

## Entwurf

- Abhängigkeiten per Konstruktor injizieren. Kein `new` für Services, keine statischen Aufrufe mit Zustand, kein globaler Zustand.
- Kleine Klassen mit einer Aufgabe. Fachlogik gehört in Domänen- oder Anwendungsdienste, nicht in Controller, Konsolenbefehle oder Listener.
- Eigene, sprechende Exceptions statt generischer. Fehler nicht verschlucken, Exceptions nicht zur normalen Ablaufsteuerung nutzen.
- `null` bewusst einsetzen: Rückgabetyp `?Foo` nur, wenn "nicht vorhanden" ein normaler Fall ist, sonst Exception.
- Entwurfsmuster nur bei konkretem Problem. Referenz: Skill `php-agents:php-design-patterns`.

## Datenbank und Doctrine

- Zugriff über Repositories, keine verstreuten Queries. Parameter binden, nie Werte in SQL/DQL konkatenieren.
- Keine Queries in Schleifen (N+1): Beziehungen per Join mit `addSelect` laden oder in Blöcken abfragen.
- Mehrteilige Schreibvorgänge in einer Transaktion. `flush()` gebündelt, nicht pro Objekt in einer Schleife.
- Große Mengen in Blöcken verarbeiten und den Speicher mit `clear()` freigeben.
- Schema-Änderungen nur über Migrationen. Jede Migration vor dem Ausführen prüfen lassen (Agent `php-agents:doctrine-migration-reviewer`). Entity-Mapping und Migration müssen übereinstimmen.

## Tests und Werkzeuge

- Befehle aus `composer.json` (`scripts`) oder `Makefile` übernehmen. Static Analysis (PHPStan/Psalm) und Code-Style (ECS/PHP-CS-Fixer) mit der Konfiguration des Projekts.
- PHPUnit: Arrange-Act-Assert, ein Verhalten pro Test, sprechende Testnamen. Mocks nur an den Grenzen (I/O, Zeit, Netz), eigene Wertobjekte nicht mocken.
- Unit-Tests ohne Datenbank. Datenbankzugriffe in Integrationstests gegen die Test-Datenbank.
- Edge Cases abdecken: leere Werte, Grenzwerte, Fehlerpfade.
- Findest du beim Testen einen Bug im Produktionscode, melde ihn und behebe ihn nicht ungefragt.
