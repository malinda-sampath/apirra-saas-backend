# Apirra backend — CLAUDE.md

Shared project context for anyone (human or AI agent) working in this repo.
Keep it short and true. Update it in the same PR as any change that makes it wrong.

## What this is
Backend for **Apirra**, an open-source, browser-based API explorer (Swagger-style exploring,
Postman-style saved requests). Guests use the explorer without an account; signed-in users save
collections, requests and environments. The frontend lives in a separate repo
(`apirra-saas-frontend`). License: Apache-2.0. Contributions need DCO sign-off.

Status: Phase 0 (foundations). The work breakdown and Apirra-Architecture.pdf (v1.3) are the
source of truth for scope and design. Do not build ahead of the current task.

## Repo layout
- `apirra-api/`    Spring Boot modular monolith (accounts, sessions, saved data, proxy tokens)
- `apirra-proxy/`  (Phase 3) isolated service that runs "Try it" requests; no database
- `infra/`         local Docker Compose (Postgres, Mailpit)
- `docs/adr/`      Architecture Decision Records
Each service is an independent Maven project. There is no parent POM on purpose.

## Stack (decided — see docs/adr)
Java 25 · Spring Boot 4.2.x · Maven wrapper · PostgreSQL 18 · Flyway · Spring Modulith ·
Spring Security + Spring Session JDBC · Testcontainers · virtual threads on.

## Commands (run inside apirra-api/)
- Build and all tests: `./mvnw verify`
- Unit tests only: `./mvnw test`
- Run locally: `./mvnw spring-boot:run` (needs `infra/` compose up once the DB exists)
Tests must pass with nothing running locally except Docker.

## Architecture rules (non-negotiable)
1. **Modules:** `identity`, `workspace`, `common` under `com.apirra.api`. A module exposes a small
   public API in its root package; everything else is internal. Never read another module's
   tables, entities or repositories. `identity` → `workspace` communication is events only
   (`UserRegistered`, `UserDeleted`). `common` holds technical helpers, never business logic.
   `ApplicationModules.verify()` must pass.
2. **Database:** one database `apirra`, one schema per module plus `platform` for
   infrastructure tables. No foreign keys across schemas. Schema changes only through new
   Flyway migrations; never edit an applied migration. Hibernate runs with `ddl-auto: validate`.
3. **API:** under `/api/v1`, plural nouns, camelCase JSON. Return DTOs, never entities. Errors
   are RFC 9457 Problem Details (`application/problem+json`); no custom response wrapper, no
   stack traces or SQL in responses. Updates use ETag + `If-Match` (412 stale, 428 missing).
   Another user's resource returns 404.
4. **Auth:** server-side sessions in an HttpOnly `__Host-` cookie + CSRF token. No JWTs in the
   browser. Passwords hashed with Argon2id. Responses never reveal whether an email exists.
5. **Security:** the API never fetches user-supplied URLs (only apirra-proxy makes outbound
   requests). Secrets only from environment variables; no default values for secrets; startup
   fails if one is missing. Never log secrets, tokens, request/response bodies or raw emails.
6. **Topology:** SPA and API share one origin (`/` and `/api`), so no CORS on the API.

## Testing
- Unit tests for pure logic; `@WebMvcTest` / `@DataJpaTest` slices; `@ApplicationModuleTest`
  per module; Testcontainers (postgres:18-alpine) for integration tests.
- Every endpoint gets: an unauthenticated test, a missing-CSRF test (state-changing calls) and
  a second-user (ownership) test.
- Never weaken, skip or delete a test to make a build pass.

## Workflow
- Full rules: CONTRIBUTING.md. `main` is protected: changes go through PRs, squash-merged.
  Releases are tags (`v0.1.0`).
- Branches: `<type>/<short-name>` (feat, fix, chore, docs, refactor, test, ci, build).
- Every commit is signed off: `git commit -s` (DCO check must pass).
- One task (WBS ID) per PR. PR title: `<type>: <WBS ID> <summary>`, e.g.
  `chore: 0.3 add modules and Modulith`.
- New dependency → justify it in the PR description. Significant decision → new ADR.

## Definition of done
`./mvnw verify` green · new logic tested · no secrets committed · docs/ADR updated if behaviour
or a decision changed · the task's "done when" in the work breakdown is met.
