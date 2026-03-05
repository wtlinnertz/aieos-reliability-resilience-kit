# Reliability & Resilience Kit — Documentation Index

This kit governs how production systems are monitored, measured, and kept running. It is Layer 6 of the AIEOS system.

---

## Start Here

| Document | Purpose |
|----------|---------|
| `playbook.md` | End-to-end process definition — read this first |
| `how-to-use-with-ai.md` | AI session setup and tool guidance |
| `how-to-adapt.md` | Organizational adoption guidance |
| `governance-model.md` | AIEOS structural rules (reference) |

---

## Artifact Governing Files

### Step 0 — Service Reliability Entry Record (SRER)

| File | Location | Purpose |
|------|----------|---------|
| Spec | `specs/srer-spec.md` | Content rules and 5 hard gates |
| Template | `artifacts/srer-template.md` | Entry record structure |
| Prompt | *(human-authored — no generation prompt)* | — |
| Validator | `validators/srer-validator.md` | Pass/fail evaluation |

### Step 1 — Service Reliability Profile (SRP)

| File | Location | Purpose |
|------|----------|---------|
| Spec | `specs/srp-spec.md` | Content rules and 7 hard gates |
| Template | `artifacts/srp-template.md` | SRP structure |
| Intake | `artifacts/service-reliability-intake-template.md` | Human input for SRP generation |
| Prompt | `prompts/srp-prompt.md` | Generation instructions |
| Validator | `validators/srp-validator.md` | Pass/fail evaluation |

### Step 2 — Incident Record (IR)

| File | Location | Purpose |
|------|----------|---------|
| Spec | `specs/ir-spec.md` | Content rules and 6 hard gates |
| Template | `artifacts/ir-template.md` | IR structure |
| Prompt | `prompts/ir-prompt.md` | Generation instructions |
| Validator | `validators/ir-validator.md` | Pass/fail evaluation |

### Step 3 — Reliability Health Report (RHR)

| File | Location | Purpose |
|------|----------|---------|
| Spec | `specs/rhr-spec.md` | Content rules and 5 hard gates |
| Template | `artifacts/rhr-template.md` | RHR structure |
| Prompt | `prompts/rhr-prompt.md` | Generation instructions |
| Validator | `validators/rhr-validator.md` | Pass/fail evaluation |

---

## Utility Prompts

| File | Purpose |
|------|---------|
| `prompts/incident-triage-prompt.md` | Adversarial incident analysis (non-governed) |
| `prompts/slo-calibration-prompt.md` | SLO target recommendations from baseline data (non-governed) |
| `prompts/escalation-assessment-prompt.md` | Assess IR or RHR against the four AIEOS escalation triggers; produce escalation record if triggered (non-governed) |
| `prompts/portfolio-health-prompt.md` | Synthesize N RHRs across services for portfolio-level reliability visibility (non-governed) |

---

## Principles

| File | Purpose |
|------|---------|
| `principles/service-reliability-principles.md` | SLO philosophy, error budget rules, observability standards |
| `principles/incident-management-principles.md` | Incident classification, response escalation, blameless review |

---

## Examples

`examples/basic-operation/` — Complete worked example: TaskFlow notification-service operation

| File | Contents |
|------|---------|
| `README.md` | Scenario description and navigation guide |
| `00-service-reliability-entry.md` | SRER (entry gate) |
| `01-srp.md` | Service Reliability Profile |
| `02-incident-record.md` | Incident Record (SEV3) |
| `03-rhr.md` | Reliability Health Report (30-day) |
| `validator-outputs/` | PASS JSON for all four artifacts |

---

## Tests

`tests/kit-test-plan.md` — S-01 to S-08 structural checks + F-00 to F-02 flow scenarios
