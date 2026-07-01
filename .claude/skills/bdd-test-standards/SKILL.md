---
name: bdd-test-standards
description: "Use whenever writing or reviewing unit tests, integration tests, or regression tests. Trigger on any test creation task, bug fixes (regression test required), and new feature work. Covers Given-When-Then structure, JUnit 5 and Mockito conventions."
---

# BDD test standards (Given-When-Then)

## Structure every test method as three commented blocks

```
@Test
void shouldReturnConflictWhenOrderAlreadyShipped() {
    // Given
    ...setup...

    // When
    ...the action under test...

    // Then
    ...assertions...
}
```

Test method names describe the behavior, not the mechanism: `shouldRejectDuplicateOrder`, not `testOrder2`.

## Test type definitions (fill in with your team's exact boundaries if these differ)

- **Unit test** — single class in isolation, all dependencies mocked (Mockito). Fast, no Spring context, no DB.
- **Integration test** — real Spring context (`@SpringBootTest`), real or test-container-backed MySQL/DocumentDB, verifies components work together.
- **Regression test** — a test added specifically because a bug occurred, asserting the exact previously-broken behavior is now correct. Named to reference the bug/ticket where practical, e.g. `shouldNotDoubleChargeOnRetry_ORD1234`.

## Mandatory coverage

- Every new service method: unit test for the happy path + at least one failure/edge case.
- Every new endpoint: integration test covering the controller-to-repository path.
- Every bug fix: a regression test that fails against the old code and passes against the fix — non-negotiable, see `bug-fixer` subagent rules.
- Every new Camel route: unit test for the processor/service logic, integration test for the route (e.g. using Camel's test kit).

## Mocking conventions

- Mock only true external dependencies (repositories, external clients) — don't mock the class under test's own collaborators unnecessarily.
- Use `verify()` sparingly — prefer asserting on returned values/state over over-specifying interaction counts.

## Review checklist

- [ ] Given-When-Then structure present and readable
- [ ] Test names describe behavior
- [ ] Happy path + at least one failure case covered
- [ ] Regression test present for any bug fix
