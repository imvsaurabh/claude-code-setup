---
name: company-coding-standards
description: "Use whenever writing, editing, or reviewing Java code in this project — package naming, module structure, style rules, and forbidden patterns. Trigger on any code creation, new class/package, or code review task, including greenfield projects and casual phrasing like 'add a feature' or 'build a service'."
---

# Company coding standards

> Fill in the placeholders below with your team's actual conventions.
> This file is read by every builder, fixer, and reviewer subagent.

## Package structure

```
com.company.<service-name>
├── controller      # REST controllers only, no business logic
├── service         # Business logic, interfaces + impl
├── repository      # Spring Data repositories / DAO layer
├── model / entity  # JPA entities, DocumentDB documents
├── dto             # Request/response DTOs, never expose entities directly
├── mapper          # Entity <-> DTO mapping
├── config          # Spring configuration classes
├── exception       # Custom exceptions + global handler
└── route           # Camel routes (message-processor projects only)
```

Replace with your actual package layout if different.

## Naming conventions

- Classes: `PascalCase`, suffix by role — `OrderController`, `OrderService`, `OrderServiceImpl`, `OrderRepository`, `OrderDto`, `OrderMapper`.
- Methods: `camelCase`, verb-first — `createOrder`, `findActiveOrdersByCustomer`.
- Constants: `UPPER_SNAKE_CASE`.
- Test classes: `<ClassUnderTest>Test` for unit, `<ClassUnderTest>IT` for integration.
- Branch names: (fill in convention, e.g. `feature/ORD-123-add-status-endpoint`)
- Commit messages: (fill in convention, e.g. Conventional Commits, or ticket-ID-first)

## Layering rules

- Controllers depend only on services — never on repositories directly.
- Services depend only on repositories and other services — never on controllers.
- DTOs cross layer boundaries at the controller edge; entities never leave the service layer.
- No business logic in controllers, mappers, or DTOs.

## Forbidden patterns

- No field injection (`@Autowired` on fields) — use constructor injection.
- No catching `Exception` or `Throwable` broadly — catch specific exceptions.
- No `System.out.println` / `printStackTrace` — use the structured logger (see `structured-logging` skill).
- No magic strings/numbers for status codes, error codes, or config values — use named constants or config properties.
- No direct string concatenation for SQL/NoSQL queries — use parameterized queries / repository methods.

## Code review gate

Before considering any change "done", it must comply with this file. `code-reviewer` checks against it explicitly — see `code-review-checklist` skill.
