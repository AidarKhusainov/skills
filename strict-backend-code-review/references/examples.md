# Code Review Output Examples

Load this file only when an example is needed to resolve output ambiguity.

Do not copy examples mechanically.

Use the core `SKILL.md` output contract as the source of truth.

## Clean approve

```text
Verdict: APPROVE
Highest severity: NONE
```

## Request changes with one verified finding

```text
Verdict: REQUEST_CHANGES
Highest severity: HIGH

Findings:
1. [HIGH][Verified finding][high]
   Location:
   - PaymentEventConsumer#handle
   - payment_event table migration

   Problem:
   The consumer applies payment state transitions without durable idempotency. A redelivered event can transition the same invoice twice across pod restarts or multiple replicas.

   Evidence:
   PaymentEventConsumer#handle checks only current in-memory processing state before updating the invoice. The PR does not add a processed-event table, unique operation key, or transactionally written idempotency record. The consumer acknowledges messages after the handler returns, so broker redelivery remains realistic after failure.

   Required change:
   Add a durable processed-message or business-operation idempotency record keyed by stable event id, written in the same transaction as the invoice state transition. A duplicate event must not apply a second transition.

   Fix timing: Must fix in this PR

   Tests:
   Add a consumer-level duplicate-delivery test that processes the same event id twice against a realistic persistence boundary and proves only one invoice transition is committed.

   Category: Resilience
   Subcategory: Durable idempotency
```

## Risk hypothesis when context is incomplete

```text
Verdict: REQUEST_CHANGES
Highest severity: MEDIUM
Review completeness: partial
Reason: Consumer compatibility could not be verified because no event schema or existing producer contract was available.

Findings:
1. [MEDIUM][Risk hypothesis][medium]
   Location:
   - OrderCreatedEvent
   - OrderEventPublisher

   Problem:
   The PR adds a required event field without compatibility evidence. Existing consumers may fail if they deserialize older messages or receive new messages before they are updated.

   Evidence:
   OrderCreatedEvent now requires `fulfillmentMode`. The PR updates the producer but does not include event schema/versioning changes, consumer updates, or compatibility tests. Existing consumer contracts were not available in the reviewed context.

   Required change:
   Make the field backward-compatible during rollout or add versioned contract evidence showing old/new producer-consumer combinations are safe.

   Fix timing: Must fix in this PR

   Tests:
   Add contract tests for old message -> new consumer and new message -> existing consumer behavior, or provide equivalent schema compatibility evidence.

   Category: API Contract
   Subcategory: Event compatibility
```

## Merge-blocking question as finding

```text
Verdict: REQUEST_CHANGES
Highest severity: HIGH

Findings:
1. [HIGH][Question][medium]
   Location:
   - CustomerController#create
   - SecurityConfig

   Problem:
   The PR adds a state-changing customer creation endpoint, but the reviewed diff does not show how caller authorization is enforced. Without that information, safe merge cannot be determined.

   Evidence:
   CustomerController#create is a new POST endpoint. The diff does not add `@PreAuthorize`, route-level security config, application-service authorization, or denied-path tests. Existing security configuration for this route was not visible in the reviewed context.

   Required change:
   Show or add the project-standard authorization enforcement for this endpoint and prove unauthorized callers cannot create customers.

   Fix timing: Must fix in this PR

   Tests:
   Add API/security boundary tests for unauthenticated caller, authenticated caller without permission, and authorized caller. Assert no customer is persisted on denied paths.

   Category: Security
   Subcategory: Missing authorization evidence
```

## Comment-only review

```text
Verdict: COMMENT_ONLY
Highest severity: LOW

Findings:
1. [LOW][Verified finding][high]
   Location:
   - DiscountPolicyTest#shouldCreateOrder

   Problem:
   The test name says the order is created, but the assertion verifies discount rejection. This makes the changed behavior harder to understand during future modifications.

   Evidence:
   The test method name is `shouldCreateOrder`, while the assertions expect `DISCOUNT_REJECTED` and no persisted discounted order.

   Required change:
   Rename the test to describe the rejected-discount behavior it verifies.

   Fix timing: Can be follow-up

   Tests:
   Not required because this is a test name clarification with no behavior, contract, persistence, runtime, or security change.

   Category: Maintainability
   Subcategory: Misleading test name
```

## Good Problem field

```text
Problem:
The new PATCH mapping treats missing and explicit null values as the same update command. Clients can no longer clear `middleName` without also making omitted fields ambiguous.
```

## Bad Problem field

```text
Problem:
Patch handling is not clean.
```

## Good Evidence field

```text
Evidence:
CustomerPatchRequest uses nullable fields for both absent and explicit null values. CustomerMapper#toCommand maps both cases to `null`, and CustomerControllerTest covers only the happy path with all fields present.
```

## Bad Evidence field

```text
Evidence:
This usually causes bugs.
```

## Good Required change

```text
Required change:
Represent missing and explicit null separately in the PATCH command and preserve the existing value when a field is absent. Explicit null should clear only fields where clearing is allowed by the API contract.
```

## Bad Required change

```text
Required change:
Improve PATCH handling.
```

## Good Tests field

```text
Tests:
Add API boundary tests for missing field, explicit null, and valid value in CustomerPatchRequest. Assert the persisted customer state and response body for each case.
```

## Bad Tests field

```text
Tests:
Add tests.
```

## When to omit a finding

Omit if:
- evidence is vague;
- issue is unrelated legacy code;
- formatter/linter/CI already reports it clearly;
- issue is a style preference;
- risk is not realistic for the changed surface;
- required change would be broader than the proven root cause.
