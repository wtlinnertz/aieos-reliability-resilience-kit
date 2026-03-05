# Reliability & Resilience Kit — Test Plan

This document contains the structural integrity checks and flow scenario tests for the Reliability & Resilience Kit. These tests verify that the kit is complete, internally consistent, and capable of producing valid artifacts.

---

## Structural Integrity Checks

Structural checks verify that the kit's files are present, properly named, and internally consistent. These checks do not require AI — they are verifiable by inspection.

### S-01: Four-File Completeness

**Check:** Every governed artifact type has exactly four files: spec, template, prompt, validator.

| Artifact Type | Spec | Template | Prompt | Validator |
|---------------|------|----------|--------|-----------|
| SRER | docs/specs/srer-spec.md | docs/artifacts/srer-template.md | *(human-authored — no prompt)* | docs/validators/srer-validator.md |
| SRP | docs/specs/srp-spec.md | docs/artifacts/srp-template.md | docs/prompts/srp-prompt.md | docs/validators/srp-validator.md |
| IR | docs/specs/ir-spec.md | docs/artifacts/ir-template.md | docs/prompts/ir-prompt.md | docs/validators/ir-validator.md |
| RHR | docs/specs/rhr-spec.md | docs/artifacts/rhr-template.md | docs/prompts/rhr-prompt.md | docs/validators/rhr-validator.md |

**Expected result:** All files present.

*Note: The SRER is human-authored and does not have a generation prompt. This is consistent with the entry gate pattern (see REK Release Entry Record). The spec, template, and validator constitute three of the four files; the fourth is intentionally absent for human-authored entry gates.*

---

### S-02: Hard Gate Count Alignment

**Check:** Each spec's declared hard gate count matches the validator's gate checks.

| Artifact Type | Spec Hard Gates | Validator Gates |
|---------------|----------------|----------------|
| SRER | 5 | 5 |
| SRP | 7 | 7 |
| IR | 6 | 6 |
| RHR | 5 | 5 |

**Expected result:** Counts match for all four artifact types.

---

### S-03: Hard Gate Name Alignment

**Check:** Gate names in specs match gate names in validators (exact string match for JSON output field names).

| Artifact | Spec Gate Names | Validator Gate Names |
|----------|----------------|---------------------|
| SRER | document_control, upstream_reference, reliability_owner, initial_slo_baseline, monitoring_confirmed | document_control, upstream_reference, reliability_owner, initial_slo_baseline, monitoring_confirmed |
| SRP | document_control, slo_definitions, error_budget, burn_rate_alerts, measurement_methodology, exclusions, standard_enforceability | document_control, slo_definitions, error_budget, burn_rate_alerts, measurement_methodology, exclusions, standard_enforceability |
| IR | incident_classification, impact_statement, timeline, root_cause, remediation, slo_impact | incident_classification, impact_statement, timeline, root_cause, remediation, slo_impact |
| RHR | review_period, slo_compliance, error_budget_state, incident_summary, layer7_feed | review_period, slo_compliance, error_budget_state, incident_summary, layer7_feed |

**Expected result:** All gate names match exactly.

---

### S-04: Prompt-to-Spec Reference Integrity

**Check:** Each generation prompt references the correct spec and template. No prompt inlines content rules.

| Prompt | References Spec | References Template | Inlines Rules? |
|--------|----------------|--------------------|----|
| srp-prompt.md | docs/specs/srp-spec.md | docs/artifacts/srp-template.md | No |
| ir-prompt.md | docs/specs/ir-spec.md | docs/artifacts/ir-template.md | No |
| rhr-prompt.md | docs/specs/rhr-spec.md | docs/artifacts/rhr-template.md | No |

**Expected result:** All prompts reference correct spec and template; no inlined rules.

---

### S-05: Validator-to-Spec Reference Integrity

**Check:** Each validator references its spec as the source of truth. Validators do not reference prompts.

| Validator | References Spec | References Prompt? |
|-----------|-----------------|-------------------|
| srer-validator.md | docs/specs/srer-spec.md | No |
| srp-validator.md | docs/specs/srp-spec.md | No |
| ir-validator.md | docs/specs/ir-spec.md | No |
| rhr-validator.md | docs/specs/rhr-spec.md | No |

**Expected result:** All validators reference the correct spec; none reference prompts.

---

### S-06: Template Section Alignment

**Check:** Each template's section headings match the required sections listed in the corresponding spec.

| Artifact | Spec Required Sections | Template Sections |
|----------|----------------------|-------------------|
| SRER | Document Control, Upstream Reference, Reliability Owner, Initial SLO Baseline, Monitoring Confirmation, Completeness Checklist, Freeze Declaration | §1–§7 (all present) |
| SRP | Document Control, SLO Definitions, Error Budget, Burn Rate Alerts, Measurement Methodology, Exclusions, Scope and Exceptions | §1–§7 (all present) |
| IR | Document Control, Incident Classification, Impact Statement, Incident Timeline, Root Cause Analysis, Remediation, SLO Impact | §1–§7 (all present) |
| RHR | Document Control, SLO Compliance, Error Budget State, Incident Summary, Handoff to Layer 7 | §1–§5 (all present) |

**Expected result:** All template sections match spec required sections.

---

### S-07: Utility Prompt Identification

**Check:** Utility prompts are clearly identified as non-governed (no spec, no validator, no template). Neither utility prompt is referenced as a governed artifact anywhere in the kit.

| Utility Prompt | Non-Governed Label Present | Has Spec? | Has Validator? |
|----------------|--------------------------|-----------|----------------|
| incident-triage-prompt.md | Yes ("Type: Utility prompt (non-governed)") | No | No |
| slo-calibration-prompt.md | Yes ("Type: Utility prompt (non-governed)") | No | No |

**Expected result:** Both utility prompts are labeled non-governed; neither has a spec or validator.

---

### S-08: Example Artifact Coverage

**Check:** The worked example covers all four artifact types with corresponding validator outputs.

| Example File | Artifact Type | Validator Output |
|-------------|---------------|----------------|
| examples/basic-operation/00-service-reliability-entry.md | SRER | validator-outputs/srer-validation.json |
| examples/basic-operation/01-srp.md | SRP | validator-outputs/srp-validation.json |
| examples/basic-operation/02-incident-record.md | IR | validator-outputs/ir-validation.json |
| examples/basic-operation/03-rhr.md | RHR | validator-outputs/rhr-validation.json |

**Expected result:** All four artifact types represented; all four validator outputs present with PASS status.

---

## Flow Scenario Tests

Flow scenarios verify that the kit's artifacts, when produced in order with appropriate inputs, pass validation. These tests require AI execution.

---

### F-00: Normal Operation Flow

**Scenario:** Receive a frozen RR → complete SRER → generate and freeze SRP → monitor for 30 days (no incidents) → generate and freeze RHR.

**Setup:**
- Provide: a frozen RR with complete §7 Handoff (use the example RR-TASKFLOW-001 as a template)
- Generate SRER manually using the SRER template
- Provide intake form with 2+ SLOs, exact metric names, and budget policy
- Validate SRER → freeze → generate SRP → validate → freeze
- Provide monitoring data showing all SLOs met, no incidents
- Generate RHR → validate → freeze

**Expected outcomes:**
- SRER: all 5 gates PASS
- SRP: all 7 gates PASS
- RHR: all 5 gates PASS; incident summary shows zero incidents across all severity levels

**Key gate to verify:** RHR Gate 4 (incident_summary) — confirm that "no incidents" is handled correctly: total = 0, all four severity levels shown as 0, pattern observation states "no patterns identified" with explanation.

---

### F-01: Incident During Operation

**Scenario:** Service is in operation with a frozen SRP → SEV3 incident occurs → generate IR → freeze IR → generate RHR that includes the incident.

**Setup:**
- Use frozen SRP from F-00 or the example SRP-NOTIF-001
- Provide incident evidence: a delivery rate drop (similar to IR-NOTIF-001 pattern)
- Generate IR using the IR prompt
- Validate IR → freeze
- Generate RHR covering the period including the incident
- Validate RHR

**Expected outcomes:**
- IR: all 6 gates PASS; severity is SEV3 (from enumerated list); SLO impact calculated with time equivalent and remaining budget
- RHR: all 5 gates PASS; incident referenced by IR ID; incident summary shows SEV3: 1; delivery rate compliance reflects the incident

**Key gate to verify:** IR Gate 4 (root_cause) — confirm contributing factor beyond "human error" is present; systemic factors assessed.

---

### F-02: SRP Revision After SLO Change

**Scenario:** Service has been running for 2 months. The delivery rate SLO target is being tightened from 99.0% to 99.5% based on post-incident review findings. Generate a new SRP version and generate an RHR that spans the version change.

**Setup:**
- Use frozen SRP v1 (SRP-NOTIF-001 v1)
- Provide an intake form reflecting the changed target (delivery rate: 99.5%)
- Generate SRP v2 using the SRP prompt
- Validate SRP v2 → freeze
- Generate RHR covering a period that spans both v1 and v2 active dates
- Validate RHR

**Expected outcomes:**
- SRP v2: all 7 gates PASS; version is v2; error budget recalculated for new target (0.5% = 216 minutes/30 days); burn rate alert thresholds adjusted proportionally
- RHR: all 5 gates PASS; both SRP versions referenced in Document Control; SLO compliance reported separately for v1 sub-period and v2 sub-period

**Key gate to verify:** RHR Gate 1 (review_period) — confirm that both SRP versions are referenced when a version change occurred during the coverage period.

---

## Notes

- All structural checks (S-01 through S-08) should be verified before running flow scenarios.
- Flow scenarios F-00 through F-02 cover the three most common operational patterns.
- Additional scenarios may be added as new patterns are identified in production use.
- Example artifacts (basic-operation/) are the reference implementation; they should pass all validators without modification.
