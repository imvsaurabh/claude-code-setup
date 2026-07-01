# Project memory — Java backend services

This file is read automatically at the start of every Claude Code session.
Keep it short. Detailed procedures live in `.claude/skills/`, not here.

## Stack

- Language: Java (fill in version)
- Framework: Spring Boot
- Messaging: Apache Camel + Solace PubSub+
- Databases: MySQL, Amazon DocumentDB
- Testing: JUnit 5, Mockito, BDD (Given-When-Then)
- Bug tracking: Azure DevOps (tickets pasted manually into chat — no live connector)

## Non-negotiable rules

1. Never commit directly to `main`/`master`. Always work on a feature branch.
2. Never `git push --force` without explicit confirmation.
3. Every code change that fixes a bug must include a regression test proving the bug is closed.
4. Every new endpoint or message route must include unit + integration tests before being considered done.
5. Structured logging only — use the `v("key", "value")` style. Never log raw string concatenation. Never log secrets, tokens, or full PII payloads.
6. Follow package naming and layering conventions in `company-coding-standards` skill — do not improvise structure.

## Before planning anything new (greenfield feature, new service, new route)

Before presenting a plan, load and apply, in this order:
1. `company-coding-standards`
2. `design-patterns-checklist`
3. The relevant stack skill — `spring-boot-rest-conventions` for REST work, `apache-camel-conventions` for message processing
4. `structured-logging`

Do this even if the request is phrased casually (e.g. "add an endpoint for X") — don't wait for the word "new" or "scaffold" to trigger it.

## Subagents available

| Subagent | Use for |
|---|---|
| `api-builder` | New/changed REST endpoints |
| `message-processor-builder` | New/changed Camel routes |
| `bug-fixer` | Fixing a defect from a pasted Azure DevOps ticket |
| `test-writer` | Writing/updating unit, integration, regression tests |
| `code-reviewer` | Read-only review pass — never edits code |
| `security-scanner` | Read-only vulnerability scan — never edits code |
| `security-fixer` | Applies only approved findings from `security-scanner`'s report |
| `plan-reviewer` | Read-only check of a plan against standards before approval |

Invoke the narrowest subagent for the job. Don't let the main agent do a builder's or reviewer's job itself when a subagent exists for it.

## Workflow expectations

- Use plan mode for anything touching more than one file or any endpoint/route/schema change. Small, isolated fixes can skip plan mode.
- `code-reviewer` and `security-scanner` are read-only by design. Never give them write access. Findings go back to the human (or to `security-fixer`, which only acts on approved findings) — never auto-applied.
- After any `bug-fixer` run, a regression test is mandatory, not optional — confirm one exists before calling the fix done.
- Hooks (see `.claude/hooks/HOOKS.md`) handle formatting, linting, test runs, and secret scanning automatically. Don't manually re-explain these steps in prompts — they run regardless.

## Definition of done (applies to every task)

- [ ] Code follows `company-coding-standards` and `design-patterns-checklist`
- [ ] Structured logging used correctly, no sensitive data logged
- [ ] Tests written in Given-When-Then form, passing
- [ ] `code-reviewer` pass completed with no unresolved findings
- [ ] No secrets or new high/critical CVEs introduced (hooks + `security-scanner` as needed)
