---
name: spring-boot-rest-conventions
description: "Use whenever creating or modifying a REST API in Spring Boot — new endpoints, controllers, services, repositories, DTOs, or exception handling. Trigger on greenfield REST services as well as adding a feature to an existing one, for both MySQL and Amazon DocumentDB backed resources."
---

# Spring Boot REST conventions

## Controller layer

- One controller per resource. Thin — delegates to a service, no logic.
- Use `@RestController`, constructor injection, `ResponseEntity<T>` return types.
- Validate request bodies with `@Valid` + Bean Validation annotations on the DTO, not manual checks in the controller.
- Endpoint naming: plural nouns, resource-based — `/orders`, `/orders/{id}`, `/orders/{id}/status`. No verbs in the path.

## Service layer

- Interface + implementation (`OrderService` / `OrderServiceImpl`) unless the team has moved away from this — check `company-coding-standards` for the current stance.
- All business rules and validation beyond basic field checks live here, not in the controller or repository.
- Services throw domain-specific exceptions (e.g. `OrderNotFoundException`), never raw `RuntimeException`.

## Repository layer

- MySQL: Spring Data JPA repositories, method-name queries or `@Query` with named parameters — never string-concatenated queries.
- DocumentDB: Spring Data MongoDB-style repositories or the driver directly, depending on project setup. Sanitize any dynamic filter construction — never build a `$where` clause from unsanitized input.

## DTOs and mapping

- Never expose JPA entities or DocumentDB documents directly in a response.
- Request DTO and response DTO are separate types even if they look similar today — they will diverge.
- Mapping logic lives in a dedicated mapper class (or MapStruct), not inline in the controller or service.

## Error handling

- One `@ControllerAdvice` / `@RestControllerAdvice` global exception handler per service.
- Domain exceptions map to specific HTTP status codes (404 for not-found, 409 for conflict, 400 for validation, etc.) — never a blanket 500 for expected failure cases.
- Error response body is a consistent shape across the whole API (fill in your standard error DTO shape here).

## New endpoint checklist

- [ ] Controller method + request/response DTOs
- [ ] Service method with business logic
- [ ] Repository method (if new query needed)
- [ ] Exception handling for expected failure cases
- [ ] Structured logging at entry/exit and on failure (see `structured-logging`)
- [ ] Unit tests (service) + integration test (controller-to-repository) — see `bdd-test-standards`
