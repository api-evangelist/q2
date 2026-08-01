---
name: Link an external account and fund via ACH
description: Add and verify an external bank account, then move money with an ACH transfer.
api: Helix by Q2
operations: [externalaccountinitiate, externalaccountverify, externalaccountget, transfercreate, transferachoriginationcreate, transactionget, transfervoid]
source: https://docs.helix.q2.com/reference
---

# Link an external account and fund a Helix account via ACH

Connect a customer's outside bank account, verify it with micro-deposits, and originate an ACH transfer.

## Auth & environment
- HTTP Basic (API Key + Secret). In sandbox use routing number `123456789` (sets the external account name to "COREPRO SANDBOX BANK"); trial deposits are hardcoded to `$0.18` and `$0.28` and can be verified immediately. See `sandbox/q2-sandbox.yml`.

## Steps
1. **Link the external account** — call `externalaccountinitiate` (programs requiring verification) or `externalaccountcreate` with the `customerId`, routing and account numbers.
2. **Verify** — call `externalaccountverify` with the two micro-deposit trial amounts. If needed, `externalaccountresendverification` re-sends deposits.
3. **Confirm** — call `externalaccountget` (or `externalaccountgetbytag`) and check `status`.
4. **Move funds** — call `transfercreate` to move money between the external and Helix accounts; use `transferachoriginationcreate` for an ODFI-originated ACH debit/credit.
5. **Reconcile** — poll `transactionget` for the resulting transaction status; `transfervoid` cancels a not-yet-settled transfer.

## Conventions & errors
- ACH settlement follows the Transfer Timeline in production; in sandbox deposits auto-settle hourly. Program-configuration errors surface as `59xxx` codes. See `errors/q2-error-codes.yml`.
- On retry, look up by `tag` before re-issuing a transfer (no idempotency-key header). See `conventions/q2-conventions.yml`.
- Subscribe to `customer-account-transfer` and `notification-of-change` events. See `asyncapi/q2-events-webhooks.yml`.
