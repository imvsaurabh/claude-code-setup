---
name: code-review-checklist
description: "Use whenever performing a code review pass on a diff or completed change. Trigger for the code-reviewer subagent and any explicit review request. Combines standards, design pattern, and logging checks into one pass."
---

# Code review checklist

Run through every section below against the diff. Report findings grouped by category with severity (blocker / suggestion). Do not fix anything — report only.

## Standards compliance (`company-coding-standards`)

- [ ] Package placement correct for each new/changed class
- [ ] Naming conventions followed
- [ ] Layering respected (no controller-to-repository shortcuts, no business logic leaking into DTOs/mappers)
- [ ] No forbidden patterns (field injection, broad exception catching, string-concatenated logs/queries)

## Design and patterns (`design-patterns-checklist`)

- [ ] SOLID violations checked, especially Single Responsibility and Dependency Inversion
- [ ] No anti-patterns introduced (god classes, deep inheritance, anemic models)
- [ ] Appropriate pattern used where the problem calls for one — not forced where unnecessary

## Logging (`structured-logging`)

- [ ] Structured arguments used, not string concatenation
- [ ] Mandatory fields present
- [ ] Correct log level
- [ ] No sensitive data logged

## Testing (`bdd-test-standards`)

- [ ] Given-When-Then structure present
- [ ] Happy path + failure case covered for new logic
- [ ] Regression test present if this diff is a bug fix

## General correctness

- [ ] Error handling covers realistic failure modes, not just the happy path
- [ ] No obvious null-pointer / unchecked-cast risks introduced
- [ ] No dead code or leftover debug statements

## Output format

Report as: `[BLOCKER|SUGGESTION] <file>:<area> — <what's wrong> — <what standard/skill it violates>`
