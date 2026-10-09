---
name: php-security-checklist
description: Use for security reviews of PHP code in any framework. Generic checklist for injection, XSS, deserialization, uploads, authentication, secrets and dependencies, with report format.
---

# PHP-Sicherheitscheckliste

Lade zuerst per Skill-Tool `developer-workflow-agents:engineering-conventions`, falls noch nicht geschehen (Secrets nie ausgeben).

## Prüfpunkte

- **SQL-Injection:** gebundene Parameter bzw. Prepared Statements, kein String-Zusammenbau in SQL/DQL.
- **Command Injection:** kein `exec`, `shell_exec`, `system` oder `proc_open` mit Nutzereingaben. Sonst feste Befehlsliste und `escapeshellarg`.
- **XSS:** Ausgabe kontextgerecht escapen (HTML, Attribut, JavaScript, URL). Rohausgabe nur für bereinigte, vertrauenswürdige Inhalte.
- **Unsichere Deserialisierung:** kein `unserialize()` auf Nutzerdaten, sonst mit `allowed_classes`. JSON bevorzugen.
- **Datei-Uploads:** Typ serverseitig prüfen, Dateinamen nicht übernehmen, Zielpfad selbst festlegen, außerhalb des Webroots speichern.
- **Pfad-Traversal:** Pfade normalisieren und gegen das Basisverzeichnis prüfen.
- **SSRF und Open Redirect:** Ziel-URLs aus Nutzereingaben gegen eine Allowlist prüfen.
- **Authentifizierung:** `password_hash` und `password_verify`, Zufall mit `random_bytes`, Token-Vergleich mit `hash_equals`, lose Vergleiche (`==`) bei Geheimnissen vermeiden.
- **Zugriffskontrolle:** serverseitig und pro Objekt prüfen, nicht nur pro Route.
- **Sessions und Cookies:** `HttpOnly`, `Secure`, `SameSite`.
- **Fehlerausgabe:** keine Stacktraces oder Debug-Ausgaben in Produktion.
- **Secrets:** Code, Konfiguration und Git-Historie auf Zugangsdaten prüfen. Gefundene Werte niemals ausgeben, nur die Fundstelle nennen.
- **Abhängigkeiten:** `composer audit`, Pakete mit bekannten Lücken.
- **Zahlungen und Webhooks:** Signatur prüfen, Idempotenz sicherstellen, Beträge serverseitig berechnen.
- **Server und Docker:** offene Ports, Debug-Modus, Standard-Passwörter.

## Rückgabe

Nach Schwere (kritisch, hoch, mittel, niedrig): `datei:zeile`, Problem, Auswirkung, Fix-Vorschlag. Nichts gefunden: sagen, was geprüft wurde.
