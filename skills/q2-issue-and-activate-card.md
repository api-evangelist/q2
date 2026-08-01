---
name: Issue and activate a card
description: Create a digital or physical card on a Helix account, verify it, and set controls/limits.
api: Helix by Q2
operations: [cardcreatedigital, cardorderphysical, cardinitiate, cardverify, cardget, cardcontrolcreate, cardlimitoverride]
source: https://docs.helix.q2.com/reference
---

# Issue and activate a Helix card

Provision a debit card against an existing Helix account and put guardrails on it.

## Auth & environment
- HTTP Basic (API Key + Secret). Debit-card transactions are **not** supported in sandbox — use the card simulation operations (`cardmock*`) to exercise flows there.

## Steps
1. **Create the card** — call `cardcreatedigital` for a digital card or `cardorderphysical` for a physical card, referencing the `customerId` and `accountId`.
2. **Initiate / activate** — call `cardinitiate`, then `cardverify` (or `cardverifybycvv`) to activate the card for use.
3. **Confirm** — call `cardget` (or `cardgetbytag`) and check the card `status`.
4. **Set controls** — call `cardcontrolcreate` to add merchant/geo/channel controls.
5. **Set limits** — call `cardlimitoverride` (or `cardlimittemporaryoverride`) to adjust spend limits; `cardlimitget` reads current limits.

## Sandbox testing
- Simulate authorizations and settlements with `cardmockwithdrawalpurchaseauthorize` / `cardmockwithdrawalpurchasecomplete`, declines with `cardmockdeclinepurchaseauthorize`, and fraud with `cardmockadminfalconfraud`. See `sandbox/q2-sandbox.yml`.

## Conventions & errors
- Watch the Events Bus for `card-modified`, `debit-card-declined`, and `visa-dps-fraud-text-alerts`. See `asyncapi/q2-events-webhooks.yml`.
- Rate limit 15 req/sec; errors in the numeric `errors[]` envelope. See `errors/q2-error-codes.yml`.
