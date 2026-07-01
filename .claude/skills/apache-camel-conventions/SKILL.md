---
name: apache-camel-conventions
description: "Use whenever creating or modifying message processors — Apache Camel routes, Solace PubSub+ producers/consumers, or Spring Boot integration for message-driven services. Trigger on greenfield message processors as well as adding a feature to an existing route."
---

# Apache Camel + Solace conventions

## Route structure

- One route class per logical flow, extending `RouteBuilder`.
- Route ID set explicitly on every route (`.routeId("...")`) — never rely on auto-generated IDs, they break log correlation.
- Keep routes declarative: routing/transformation logic in the route, business logic delegated to a service bean via `.bean(...)` or `.process(...)` calling a service — not inlined as large anonymous processors.

## Solace PubSub+ integration

- Producers: confirm delivery mode and message durability settings match the message's importance (fill in team defaults — e.g. persistent vs non-persistent per queue).
- Consumers: explicit acknowledgment mode — never rely on auto-ack for anything where message loss matters.
- Topic/queue naming: (fill in your naming convention, e.g. `<domain>.<event>.<version>`)

## Error handling

- Every route has an explicit `onException()` or dead-letter-channel strategy — no silent swallowing of exceptions.
- Distinguish retryable errors (transient network/DB issues — use redelivery policy) from non-retryable errors (bad message shape — route to DLQ immediately).
- Poison messages must not block the route indefinitely — cap retries.

## Idempotency

- Any consumer that could receive a duplicate message (at-least-once delivery) must be idempotent — use Camel's Idempotent Consumer pattern keyed on a unique message ID, backed by the same DB the service already uses.

## Database access from routes

- MySQL/DocumentDB access from within a route goes through the same service/repository layer used elsewhere in the app — never a separate ad hoc data access path inside the route.

## New route checklist

- [ ] Route ID set explicitly
- [ ] Exception handling / DLQ strategy defined
- [ ] Idempotency handled if delivery is at-least-once
- [ ] Structured logging at route entry, exit, and on error (see `structured-logging`)
- [ ] Unit test for the processor/service logic + integration test for the route itself — see `bdd-test-standards`
