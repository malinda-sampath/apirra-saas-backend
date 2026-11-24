---
description: Audit the codebase against the architecture rules (read-only)
---
Audit apirra-api against the "Architecture rules" in CLAUDE.md.

Read-only: do not modify, commit or push anything.

For each rule (modules, database, API, auth, security, topology):
- Status: follows / violates / not implemented yet.
- Evidence with file:line for every violation, and why it matters.
Also run the Modulith verification test if it exists, list each module's public API types,
and list every Flyway migration in order with a one-line summary.
Finish with the three most important problems, highest risk first.
