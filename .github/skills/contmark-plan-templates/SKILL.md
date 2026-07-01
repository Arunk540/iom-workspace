---
name: contmark-plan-templates
description: Plan output templates for each pipeline mode. Load at Stage 1 to structure plan.md correctly.
---

# Plan Templates

## Feature Mode

```markdown
# Plan: {FEATURE_NAME} [Feature]

## Stack
- **Build:** Maven | Gradle
- **Language:** Java | Kotlin
- **Stack:** WebFlux | MVC
- **CT Module:** present | absent

## Interpretation & Impact — CONFIRM before approval
> This block is the human verification gate. It highlights (a) how I read the ticket's words and (b) every repo the flow touches. Wrong? Correct it before PLAN APPROVED — drift is caught here, not after implementation.

**Vocabulary — ticket term → real code symbol** (from glossary + verified in code):

| Ticket term | Bound to (code symbol) | Values | Source (file:line) |
|---|---|---|---|
| {flow / service type} | {transportActivity} | {EXPORT\|IMPORT} | {offer-service SearchRequest.transportActivity} |

**Impact — repos in scope** (upstream + downstream analysed across the workspace):

| Repo | Direction | Why it's impacted (file:line) |
|---|---|---|
| {iom-web-integrator} | core | {builds the offer request} |
| {iom-order-service} | upstream (source) | {owns GET /v3/service-plans/containers — the count source} |
| {iom-offer-service} | downstream (consumer) | {pricing slabs evaluated per flow} |

> Terms with NO confident mapping → open questions below, never guessed. Never invent a field/method for an ungrounded term.
> **Learning:** if you correct a term mapping or acronym here, I persist the confirmed mapping to the workspace glossary (`_repo_router.json`) so future tasks resolve it automatically.

## Overview
**Objective:** {What and why — one paragraph}

### Request Flow
{ASCII diagram showing data flow: Actor → Controller → Service → Repository → Response. Adapt for Kafka/Temporal/external APIs.}

**Success Criteria:**
- [ ] {Testable business criterion}

**Boundaries:** Always: {X} | Ask first: {Y} | Never: {Z}

## Implementation Tasks
### Task 1: {Title}
- **Files:** CREATE {paths} | MODIFY {paths}
- **AC:** {What must be true when done}
- **Verify:** compiles, no checkstyle violations

## Unit Test Matrix
> One row per implementation task. `CT_MODULE: absent` skips CT only, not UT. Use canonical glossary names (e.g. `transportActivity: EXPORT|IMPORT`), never a ticket-invented name.

| # | Class Under Test | Business Scenario | Expected Behavior |
|---|------------------|-------------------|-------------------|
| 1 | {Class} | {scenario} | {expected} |

## Component Test Scenarios
> Only if CT_MODULE: present. Otherwise: ⚠️ CT Module: not found — CT Scenarios skipped.
> Mock request/response data confirmed by human before writing scenarios.

### Scenario 1: {Business outcome}
Given {precondition}
When {action}
Then {outcome — status code + business state}

## Risks
> Risks are unknowns/hazards only. A cross-repo contract or data-source dependency you DISCOVERED is NOT a risk — promote it to a companion repo in scope (append to repo_order), never park it here.
| Risk | Impact | Mitigation |
|------|--------|------------|

## Implementation Order
models → mappers → services → controller → config
```

## UT-only Mode

```markdown
# Plan: {CLASS_OR_FEATURE} [UT-only]

## Stack
- **Build:** Maven | Gradle
- **Language:** Java | Kotlin
- **Stack:** WebFlux | MVC

## Unit Test Matrix
| # | Class Under Test | Business Scenario | Expected Behavior |
|---|------------------|-------------------|-------------------|
```

## CT-only Mode

```markdown
# Plan: {FEATURE_NAME} [CT-only]

## Stack
- **Build:** Maven | Gradle
- **Language:** Java | Kotlin
- **Stack:** WebFlux | MVC
- **CT Module:** present

## Component Test Scenarios
> Mock data confirmed by human.

### Scenario 1: {Business outcome}
Given / When / Then
```

## Test Mode (UT + CT)

```markdown
# Plan: {CLASS_OR_FEATURE} [Test]

## Stack
- **Build:** Maven | Gradle
- **Language:** Java | Kotlin
- **Stack:** WebFlux | MVC
- **CT Module:** present | absent

## Unit Test Matrix
| # | Class Under Test | Business Scenario | Expected Behavior |
|---|------------------|-------------------|-------------------|
| 1 | {Class} | {scenario} | {expected} |

## Component Test Scenarios
> Only if CT_MODULE: present. Otherwise: ⚠️ CT Module: not found — CT Scenarios skipped.

### Scenario 1: {Business outcome}
Given {precondition}
When {action}
Then {outcome — status code + business state}
```

No spec, tasks, or risks section — test-only mode.
