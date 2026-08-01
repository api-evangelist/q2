---
name: Onboard a customer and open an account
description: Create a Helix customer, satisfy due-diligence, and open an FDIC-insured account.
api: Helix by Q2
operations: [customercreate, customerget, programquestionslist, customeranswerpost, accountcreate, accountget]
source: https://docs.helix.q2.com/reference
---

# Onboard a customer and open a Helix account

Use the Helix by Q2 REST API to onboard an end user and open a real, FDIC-insured account.

## Auth & environment
- Authenticate with HTTP Basic: API Key as username, Secret as password (base64 `Authorization` header). See `authentication/q2-authentication.yml`.
- Requests must originate from a whitelisted server IP (no end-user devices).
- Test in sandbox (`https://sandbox-api.helix.q2.com`) with the program-provided fake customer data before production (`https://api.helix.q2.com`).

## Steps
1. **Create the customer** — call `customercreate` (or `customercreatebusiness` for a business) with the customer's PII and an optional client `tag`. In sandbox, multiple customers may reuse a `taxId`.
2. **Confirm status** — call `customerget` (or `customergetbytag`) and read the customer `status`. Handle `Manual Review` by watching for the `manual-review-document-requested` event.
3. **Due diligence (if required by program)** — call `programquestionslist` to fetch onboarding questions, then submit answers with `customeranswerpost`.
4. **Open the account** — call `accountcreate` with the `customerId` and the program `productId`. Persist a `tag` to cross-reference your own system.
5. **Verify** — call `accountget` and confirm the account `status` is active.

## Conventions & errors
- Idempotency is not provided by a key header; on retry, look up by `tag` (`customergetbytag` / `accountgetbytag`) before re-creating. See `conventions/q2-conventions.yml`.
- Respect the 15 req/sec per-key rate limit; back off on HTTP 429. See `rate-limits/q2-rate-limits.yml`.
- Errors arrive in the `errors[]` envelope with numeric codes (e.g. `50401` bad credentials, `50403` IP not trusted). See `errors/q2-error-codes.yml`.
