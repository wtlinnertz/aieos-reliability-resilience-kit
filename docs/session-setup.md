# Session Setup — Reliability & Resilience Kit

Use this file to set up an AI session for each RRK artifact. Find the section for the artifact you are generating or validating. Follow the checklist before starting.

**Rule:** Generate and validate in separate sessions. Do not self-validate.

**RRK cadence note:** Unlike upstream kits, RRK has ongoing operations — IRs are triggered by incidents, RHRs by calendar. The SRP is established once and revised when SLO targets or measurement methodology change.

---

## Service Reliability Entry Record (SRER-{PROJECT}-{NNN})

**What you're creating:** The formal gate that establishes service reliability ownership, confirms the Release Record §7 handoff is complete, and captures the initial SLO baseline.

**Note:** The SRER is human-authored — no generation prompt. Complete it directly using the template.

**Required Inputs (confirm before starting):**
- [ ] Frozen RR (RR-{PROJECT}-{NNN}) with §7 Handoff to Layer 6 complete — Frozen and received from REK?
- [ ] Named reliability owner — Identified (specific individual, not a team)?

**Pre-Flight Gate Check (verify before completing):**
- [ ] `document_control`: ID, service name, owner (named individual), and date ready
- [ ] `upstream_reference`: Frozen RR ID on hand; §7 confirmed complete (not referenced by pointer)
- [ ] `reliability_owner`: Specific individual named
- [ ] `initial_slo_baseline`: RR §7 SLO baseline values copied directly into SRER (not referenced by pointer)
- [ ] `monitoring_confirmed`: Monitoring is live and current health status is known

**Session Setup:**
1. Use: `docs/artifacts/srer-template.md` — fill manually, no prompt
2. No validator — human review and freeze
3. After SRER is frozen, proceed to SRP generation

**Common Failure to Avoid:**
Capturing the SLO baseline as a pointer reference ("see RR §7") — the SRER must contain the actual values. RR §7 data must be copied into SRER directly.

---

## Service Reliability Profile (SRP-{SERVICE}-{NNN})

**What you're creating:** The formal definition of what "good" looks like for this service — SLO targets, error budgets, burn rate alerts, and measurement methodology. Versioned; update when targets or methodology change.

**Required Inputs (confirm before starting):**
- [ ] Frozen SRER — Frozen?
- [ ] Organizational reliability principles — Available in `docs/principles/`?

**Pre-Flight Gate Check (verify before generating):**
- [ ] `document_control`: SRP ID, version (v1.0 for first), service name, and owner planned
- [ ] `slo_definitions`: SLO targets will be derived from SRER baseline data (not aspirational)
- [ ] `error_budget`: Ready to calculate: (1 − SLO target) × measurement window
- [ ] `burn_rate_alerts`: Fast-burn and slow-burn alert thresholds will be defined
- [ ] `measurement_methodology`: Measurement source, query or indicator, and window are known
- [ ] `exclusions`: Planned maintenance and external outage exclusion criteria are defined
- [ ] `standard_enforceability`: All SRP policies will be specific and testable

**Session Setup:**
1. Load: `docs/prompts/srp-prompt.md`
2. Provide: Frozen SRER (especially the initial SLO baseline)
3. Provide: Organizational reliability principles from `docs/principles/`
4. Provide: `docs/specs/srp-spec.md` (or confirm it is in context)
5. Validate in a separate session: `docs/validators/srp-validator.md`

**SRP revision:** When SLO targets or measurement methodology change, increment the version (v1.0 → v1.1 → v2.0 for major changes). Freeze the new version; prior versions remain as historical record.

**Common Failure to Avoid:**
SLO targets stated aspirationally ("99.9% is industry standard") without derivation from SRER baseline data — set targets based on actual measured baseline; document the data source used.

---

## Incident Record (IR-{SERVICE}-{NNN})

**What you're creating:** The operational record for a specific incident — classification, impact, timeline, root cause, remediation, and SLO impact. Generated per incident.

**Required Inputs (confirm before starting):**
- [ ] Frozen SRP — Frozen? (provides the SLO measurement methodology and targets)
- [ ] Incident timeline and evidence — Available? (logs, metrics, alert history)

**Pre-Flight Gate Check (verify before generating):**
- [ ] `incident_classification`: Severity level from enumerated list (SEV1 / SEV2 / SEV3 / SEV4) — determined
- [ ] `impact_statement`: User count, error rate, duration, and geographic scope — quantifiable
- [ ] `timeline`: Key events (detection, escalation, key actions, resolution) — available from logs
- [ ] `root_cause`: Contributing factors beyond "human error" — identified
- [ ] `remediation`: Actions taken with timestamps and actors — documented
- [ ] `slo_impact`: Ready to calculate using SRP measurement methodology

**Session Setup:**
1. Load: `docs/prompts/ir-prompt.md`
2. Provide: Frozen SRP (for SLO measurement methodology and baseline)
3. Provide: Incident timeline, observability data, and alert records
4. Provide: `docs/specs/ir-spec.md` (or confirm it is in context)
5. Validate in a separate session: `docs/validators/ir-validator.md`

**SEV1/2 note:** For SEV1/2 incidents, also initiate the Operational Diagnostics Kit (ODK) — the IR is the lightweight RRK operational record; ODK adds investigation depth. See `docs/playbook.md` for escalation protocol.

**Common Failure to Avoid:**
Root cause stated only as "human error" — identify the systemic conditions that allowed the error to occur and propagate; surface why the system was vulnerable.

---

## Reliability Health Report (RHR-{SERVICE}-{NNN})

**What you're creating:** A periodic review of service reliability health — SLO compliance, error budget state, incident summary, pattern observations, and Layer 7 feed for IEK. Generated on a defined review cadence.

**Required Inputs (confirm before starting):**
- [ ] Frozen SRP — Frozen? (provides SLO targets, measurement methodology, and error budget)
- [ ] All Incident Records (IRs) in the review period — Frozen?
- [ ] Review period defined — Start and end dates known?

**Pre-Flight Gate Check (verify before generating):**
- [ ] `review_period`: Exact start and end dates of coverage period defined
- [ ] `slo_compliance`: SLO target, measured performance, and Pass/Fail for each SLO calculable
- [ ] `error_budget_state`: Budget consumed this period and remaining balance calculable
- [ ] `incident_summary`: All IRs in the coverage period identified and available by ID
- [ ] `layer7_feed`: Ready to synthesize reliability trends and patterns for §5 (even if "no patterns")

**Session Setup:**
1. Load: `docs/prompts/rhr-prompt.md`
2. Provide: Frozen SRP
3. Provide: All frozen IRs from the coverage period (by ID and content)
4. Provide: Review period dates and any operational notes from the period
5. Provide: `docs/specs/rhr-spec.md` (or confirm it is in context)
6. Validate in a separate session: `docs/validators/rhr-validator.md`
7. After PASS: if ≥2 frozen RHRs exist, consider initiating Insight & Evolution Kit (IEK)

**Common Failure to Avoid:**
Leaving §5 Layer 7 Feed blank when there are patterns to observe — even "No patterns identified this period" must be written. A blank section fails the layer7_feed gate.
