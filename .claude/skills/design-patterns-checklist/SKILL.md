---
name: design-patterns-checklist
description: "Use when designing or reviewing code structure — class responsibilities, dependency direction, and whether a design pattern is appropriate. Trigger during planning of any new feature, service, or route, and during code review, to check SOLID compliance and expected patterns are followed."
---

# Design patterns and SOLID checklist

## SOLID self-audit (apply before finalizing any plan or diff)

- **Single Responsibility** — does this class have exactly one reason to change? Split if a class does DB access AND business rules AND formatting.
- **Open/Closed** — can new behavior be added without modifying existing tested code? Favor strategy/adapter over long `if/else` or `switch` chains on type.
- **Liskov Substitution** — does every subclass/implementation honor the contract of its interface without surprising callers?
- **Interface Segregation** — are interfaces small and role-specific, not one large interface every implementer must partially stub?
- **Dependency Inversion** — do high-level modules (services) depend on abstractions (interfaces), with concrete implementations injected — not the reverse?

## Patterns commonly expected in this codebase

(Fill in with your team's actual expectations — examples below)

- **Strategy** — for varying business rules by type (e.g. discount calculation per customer tier)
- **Builder** — for constructing complex DTOs/entities with many optional fields
- **Factory** — for creating instances where the concrete type depends on runtime input
- **Adapter** — for wrapping external service clients behind an internal interface
- **Template method** — for shared workflow with varying steps (e.g. common message processing skeleton across routes)

## Anti-patterns to flag in review

- God classes/services that grow unrelated responsibilities over time
- Anemic domain models with all logic pushed into a single "manager" or "helper" class
- Deep inheritance chains where composition would be clearer
- Utility classes accumulating unrelated static methods

## When to apply this skill

- During planning, before a plan is presented for approval, for any new class/service/route
- During `code-reviewer` and `plan-reviewer` passes
