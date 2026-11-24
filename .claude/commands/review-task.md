---
description: Review my implementation of one WBS task (read-only)
argument-hint: <task id and its "done when" text, e.g. 0.3 verify() passes>
---
Review my work for task: $ARGUMENTS

Read-only: do not modify, commit or push anything. Report problems; do not fix them.

1. Show what changed on this branch versus main (`git diff --stat main...HEAD`, then the diff).
2. Check the change against CLAUDE.md (architecture rules, testing, workflow) and against
   the task's "done when" items, one by one: met / not met / can't tell, with evidence.
3. Run `./mvnw verify` in apirra-api and report the result.
4. Check every commit has a Signed-off-by matching its author email.
5. Look for: secrets or default passwords, module boundary violations, entities returned from
   controllers, schema changes outside Flyway, missing tests for new logic, dependencies added
   but unused, work that belongs to a later task.
6. Answer in the review format from my personal instructions.
