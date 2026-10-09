---
name: php-security-checklist
description: Use for security reviews of PHP code in any framework. Generic checklist for injection, XSS, deserialization, uploads, authentication, secrets and dependencies, with report format.
---

# PHP security checklist

First load `developer-workflow-agents:engineering-conventions` through the Skill tool, if not done yet (never print secrets).

## 1. Checks

1. **SQL injection:** bound parameters or prepared statements, no string building in SQL/DQL.
2. **Command injection:** no `exec`, `shell_exec`, `system` or `proc_open` with user input. Otherwise a fixed command list and `escapeshellarg`.
3. **XSS:** escape output for its context (HTML, attribute, JavaScript, URL). Raw output only for sanitized, trusted content.
4. **Unsafe deserialization:** no `unserialize()` on user data, otherwise with `allowed_classes`. Prefer JSON.
5. **File uploads:** check the type on the server, do not take over file names, set the target path yourself, store outside the web root.
6. **Path traversal:** normalize paths and check them against the base directory.
7. **SSRF and open redirect:** check target URLs from user input against an allowlist.
8. **Authentication:** `password_hash` and `password_verify`, randomness with `random_bytes`, token comparison with `hash_equals`, avoid loose comparisons (`==`) with secrets.
9. **Access control:** check on the server and per object, not only per route.
10. **Sessions and cookies:** `HttpOnly`, `Secure`, `SameSite`.
11. **Error output:** no stack traces or debug output in production.
12. **Secrets:** check code, configuration and git history for credentials. Never print found values, only name the location.
13. **Dependencies:** `composer audit`, packages with known vulnerabilities.
14. **Payments and webhooks:** verify signatures, ensure idempotency, calculate amounts on the server.
15. **Server and Docker:** open ports, debug mode, default passwords.

## 2. Report

By severity (critical, high, medium, low): `file:line`, problem, impact, fix proposal. Nothing found: say what was checked.
