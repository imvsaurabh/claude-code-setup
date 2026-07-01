---
name: security-review
description: "Use whenever scanning code for vulnerabilities or fixing an approved security finding. Trigger for the security-scanner and security-fixer subagents. Covers OWASP Top 10 mapped to Java/Spring Boot/MySQL/DocumentDB/Camel/Solace, tool output interpretation, fix patterns, and severity rubric."
---

# Security review standard

## Scope — OWASP categories mapped to this stack

- **Injection** — native/`@Query` JPA usage with string concatenation; unsanitized filter objects in DocumentDB queries (e.g. unvalidated `$where`, operator injection via user-controlled keys).
- **Broken access control** — endpoint checks authentication but not resource ownership (e.g. any user can PATCH any order ID, not just their own). This requires reading the code, not just scanner output.
- **Insecure deserialization** — untrusted input deserialized into Java objects without validation, especially in Camel message bodies.
- **Insufficient input validation** — REST DTOs missing Bean Validation annotations; Camel routes accepting message bodies without shape/schema validation before processing.
- **SSRF** — Camel routes or service clients calling externally-supplied URIs without an allowlist.
- **Hardcoded secrets** — credentials, API keys, connection strings in source rather than config/secret manager.
- **Sensitive data exposure** — PII or tokens in structured logs (cross-check against `structured-logging`); verbose error responses exposing stack traces to clients.
- **Vulnerable dependencies** — known CVEs in `pom.xml` dependencies.

## Tooling (deterministic — run these, don't rely on LLM judgment alone)

- SAST: Semgrep (security ruleset) or SpotBugs + FindSecBugs
- Dependency CVEs: OWASP Dependency-Check (Maven plugin)
- Secrets: gitleaks

`security-scanner` runs these tools, parses their output, and adds business-logic findings (like broken ownership checks) that tools can't catch on their own.

## Severity rubric

- **Critical** — remote code execution, auth bypass, injection with data access/modification
- **High** — broken access control, hardcoded production secrets, SSRF
- **Medium** — missing input validation without a clear exploit path yet, verbose error exposure
- **Low** — outdated dependency with no known exploited CVE, minor logging hygiene issue

Critical/High findings always require explicit human approval before `security-fixer` touches them. Medium/Low can be batched for approval.

## Fix patterns (apply the idiomatic fix for this stack, not a generic patch)

- SQL injection → convert to parameterized query / Spring Data method or named-parameter `@Query`
- NoSQL injection → validate/whitelist filter keys before building the DocumentDB query
- Missing ownership check → add an explicit check that the authenticated principal owns/can access the resource, not just that they're authenticated
- Missing input validation → add Bean Validation annotations on the DTO + `@Valid` on the controller method
- Hardcoded secret → move to environment variable / secret manager, rotate the exposed credential
- Vulnerable dependency → bump to the patched version; if a major version bump, flag for human review rather than auto-applying

## Workflow

1. `security-scanner` runs tools + manual review, outputs a findings report with severity.
2. Human reviews and approves which findings to act on.
3. `security-fixer` applies only approved findings, one at a time, using the fix patterns above.
4. `test-writer` adds a regression test proving each fixed vulnerability is closed.
5. `code-reviewer` does a final pass on the resulting diff.
