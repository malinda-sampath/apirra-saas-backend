# Contributing to the Apirra backend

Thanks for helping. This guide covers how changes get into `main`. For architecture rules and
project context, read [CLAUDE.md](CLAUDE.md) first; it applies to humans too.

## Workflow

`main` is protected. Every change goes through a pull request and is squash-merged, so `main`
has one linear commit per PR. No direct pushes, no merge commits.

1. Update `main`: `git switch main` then `git pull`.
2. Create a branch from `main` (see [Branch names](#branch-names)).
3. Commit with sign-off (see [Commits](#commits)).
4. Push the branch and open a pull request into `main`.
5. Keep the branch up to date with `git pull --rebase origin main`, not a merge.
6. A maintainer squash-merges it once checks pass. The branch is then deleted.

One task (WBS ID from the work breakdown) per pull request. Keep it small.

## Branch names

Format: `<type>/<short-name>`, lowercase, words separated by hyphens.

| Prefix      | Use it for                                     | Example                          |
|-------------|------------------------------------------------|----------------------------------|
| `feat/`     | A new feature users can see                    | `feat/user-registration`         |
| `fix/`      | A bug fix                                      | `fix/session-cookie-path`        |
| `chore/`    | Maintenance with no user-visible change        | `chore/scaffold-apirra-api`      |
| `docs/`     | Documentation only                             | `docs/adr-session-auth`          |
| `refactor/` | Restructuring code without changing behaviour  | `refactor/extract-problem-mapper`|
| `test/`     | Adding or fixing tests only                    | `test/ownership-tests`           |
| `ci/`       | CI and workflow changes                        | `ci/backend-build`               |
| `build/`    | Build system and dependencies                  | `build/add-flyway`               |

The prefixes are the commit types from [Conventional Commits](https://www.conventionalcommits.org/).

## Pull request titles

Because PRs are squash-merged, the PR title becomes the commit message on `main`.
Use the same type as the branch, then the WBS ID, then a short imperative summary:

```
<type>: <WBS ID> <summary>
```

Examples:

- `chore: 0.2 scaffold apirra-api`
- `build: 0.4 add Flyway`
- `feat: 1.3 add user registration`

In the description, say what changed and why, and how you tested it. If you add a dependency,
justify it. If you make a significant design decision, add an ADR under `docs/adr/`.

## Commits

Every commit must be signed off to certify the
[Developer Certificate of Origin](https://developercertificate.org/):

```
git commit -s -m "build: add Flyway"
```

This adds a `Signed-off-by: Your Name <your@email>` line that must match the commit author.
The DCO check fails the PR otherwise. To fix a missing sign-off on your last commit, run
`git commit --amend -s --no-edit`, then `git push --force-with-lease` (never plain `--force`).

Use the same `<type>: <summary>` style for commit messages inside your branch.

## Before you open a pull request

- `./mvnw verify` passes inside the service folder (e.g. `apirra-api/`). Only Docker needs to
  be running.
- New logic has tests. Never weaken, skip or delete a test to make the build pass.
- No secrets, tokens or `.env` files are committed.
- Docs or ADRs are updated if behaviour or a decision changed.

## License

Contributions are accepted under the [Apache License 2.0](LICENSE). The Apirra name and logo are
not covered by the license; see the [README](README.md#license-and-trademark).
