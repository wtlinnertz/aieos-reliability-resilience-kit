# Incident Triage — Utility Prompt

**Type:** Utility prompt (non-governed)
**Purpose:** Adversarial analysis of an active or recent incident to prioritize response and identify gaps in the current response posture.

This prompt does not produce a governed artifact. Its output is decision support for responders during or immediately after an incident. It does not replace the Incident Record — that is generated after the incident closes.

---

## When to Use This Prompt

Use this prompt when:
- An incident is active and the response team wants a structured analysis of what is known and what gaps exist
- An incident has just resolved and the team wants to prepare for post-incident review
- An incident is ambiguous in severity and the team wants an adversarial assessment before declaring scope

Do not use this prompt to generate the IR. The IR is generated after the incident closes using the IR prompt.

---

## Inputs Required

Provide as much of the following as is available at the time of triage:

1. **Current incident state** — what is known so far: symptoms, affected components, observed metrics, user reports
2. **Active SRP** (if available) — to reason about SLO impact and burn rate
3. **Monitoring data** — any metric values, alert states, or dashboard observations available
4. **Timeline so far** — what has been tried, who is involved, what has been ruled out
5. **Context about recent changes** — deployments, config changes, dependency changes in the preceding 24–72 hours

---

## Analysis Framework

Using the inputs provided, analyze the incident across these dimensions:

### 1. Severity Assessment

Evaluate the current observed impact against the severity enumeration:

| Severity | Definition |
|----------|-----------|
| SEV1 | Complete service outage — no users can access the service or core functionality |
| SEV2 | Major degradation — significant percentage of users affected or core functionality severely impaired |
| SEV3 | Partial degradation — subset of users or non-core functionality affected; service remains available |
| SEV4 | Minor issue — minimal user impact; workaround available; not time-critical |

State:
- What severity the current evidence supports
- What evidence would confirm a higher severity
- What evidence would confirm a lower severity
- Any indicators that severity may be misclassified (escalating impact that should trigger upgrade, or overstated initial reports)

### 2. Scope Assessment

Evaluate what is and is not known about the incident scope:

- What is confirmed affected (specific functionality, user segments, regions)?
- What is suspected but not confirmed?
- What has been explicitly ruled out?
- What should be investigated but has not been yet?

Identify scope gaps: aspects of the service that could be affected but have not been checked. Flag these as investigation priorities.

### 3. SLO Impact Projection (if SRP is available)

If a frozen SRP is provided:
- Which SLOs are currently being impacted based on observed metrics?
- At the current rate, how much error budget will be consumed if the incident continues for 15 minutes? 30 minutes? 60 minutes?
- Is the freeze/slowdown condition (SRP §3) at risk of being crossed?

If no SRP is available: note this as a gap in the organization's reliability posture.

### 4. Response Gap Analysis

Evaluate the current response for gaps:

- **Detection gap**: Was this detected by monitoring or by a user report? If user report, what alert should have fired?
- **Communication gap**: Is the incident declared and ownership established? Who is the incident commander?
- **Investigation gap**: What diagnostic steps have not been taken that would narrow root cause candidates?
- **Mitigation gap**: Are there mitigation options (rollback, feature flag disable, traffic shift) that have not been attempted?
- **Escalation gap**: Does the current scope warrant escalation to additional responders or leadership?

### 5. Risk Escalation Indicators

Flag any of the following if present:
- Impact is expanding (more users, more functionality affected over time)
- Multiple mitigation attempts have failed
- Root cause is not narrowing
- Data plane changes (user data at risk)
- Contractual SLA thresholds at risk of being crossed
- External dependencies affected that may require vendor engagement

---

## Output Format

Structure your analysis as:

**Severity Assessment:** [SEV classification with confidence level and evidence basis]

**Scope — Confirmed:**
- [item]

**Scope — Suspected (not confirmed):**
- [item]

**Scope — Not yet investigated (priority gaps):**
- [item]

**SLO Impact Projection:** [budget at risk calculations if SRP available; or note if unavailable]

**Response Gaps:**
1. [gap description with recommended immediate action]
2. [gap description]

**Risk Escalation Indicators:** [list present indicators, or "None identified at current time"]

**Recommended Next Actions (priority order):**
1. [specific action]
2. [specific action]
3. [specific action]

---

## Constraints

- This output is decision support. Responders must apply judgment — do not treat this analysis as authoritative over direct system observation.
- Do not generate an IR from this output. This is triage; the IR is post-incident.
- Do not recommend actions that require information not present in the inputs.
- Flag uncertainty explicitly rather than stating uncertain conclusions as facts.
