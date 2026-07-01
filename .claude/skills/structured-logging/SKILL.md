---
name: structured-logging
description: "Use whenever writing or reviewing any log statement in Java code. Trigger on any code touching logging, error handling, or observability, and during code review to check logging compliance. Covers structured-argument style logging, e.g. v('client', 'product-service')."
---

# Structured logging standard

## Core rule

Always use structured arguments — never string-concatenated or `String.format` log messages.

Correct pattern (adjust to your actual logging library's structured-argument syntax):

```
log.info("Order status updated", v("orderId", orderId), v("newStatus", newStatus), v("client", "product-service"));
```

Never:

```
log.info("Order " + orderId + " status updated to " + newStatus);
```

## Mandatory fields

Every log line should carry, at minimum (fill in the exact field names your team uses):

- `client` or `service` — which service emitted the log
- A correlation/trace ID field, if one is available in context
- The primary entity ID relevant to the operation (`orderId`, `customerId`, etc.)

## Log level policy

- `ERROR` — unexpected failures, anything requiring investigation
- `WARN` — recoverable/expected failure paths (e.g. validation rejection, retry triggered)
- `INFO` — key business events (order created, message consumed, status changed)
- `DEBUG` — detailed flow useful in troubleshooting, not enabled by default in production

## Never log

- Passwords, tokens, API keys, session identifiers
- Full PII payloads (full name + address + payment info together) — log identifiers, not the payload
- Full request/response bodies for endpoints handling sensitive data — log a summary or reference ID instead

## Review checklist

- [ ] All log calls use structured arguments, not string concatenation
- [ ] Mandatory fields present
- [ ] Correct log level for the situation
- [ ] No sensitive data in any log statement
