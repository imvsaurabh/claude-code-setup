---
name: bug-ticket-intake
description: "Use whenever a bug report or Azure DevOps ticket is pasted into chat for fixing. Trigger before any bug investigation or fix begins, to extract and structure the ticket details."
---

# Bug ticket intake

Bug tickets are pasted manually (no live Azure DevOps connector). Before writing or changing any code, extract and restate the following from the pasted text. If a field is missing, ask for it rather than guessing.

## Fields to extract

- **Ticket ID / title** — for branch naming and regression test naming
- **Repro steps** — the exact sequence that triggers the bug
- **Expected behavior**
- **Actual behavior**
- **Environment/scope** — which service, which environment (if stated), any relevant data conditions
- **Acceptance criteria** — what "fixed" means, if stated; if not stated, infer the minimal correct behavior from expected vs actual and state the assumption

## Before starting the fix

Restate the above back in a short summary and confirm scope before editing code, e.g.:

> "Ticket ORD-1234: PATCH /orders/{id}/status returns 200 but does not persist the new status when the order is in SHIPPED state. Expected: request should either update the status or return 409 if the transition is invalid. Fix scope: OrderService.updateStatus + validation of allowed transitions."

## Handoff

Once scoped, proceed as the `bug-fixer` subagent: locate the defect, fix it, and add a regression test per `bdd-test-standards` before considering the ticket resolved.
