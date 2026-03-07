# Troubleshooting Guide — Reliability Resilience Kit

## How to Use This Guide

When a validator returns FAIL, find the failing gate in the table below. The Remediation column describes the specific fix required. Reopen the artifact, apply the remediation, and rerun the validator in a new session.

**Do not embed fix attempts in your validation session.** Validators and generation are separate sessions.

---

## Service Reliability Entry Record (SRER-{PROJECT}-{NNN})

This is a human-completed entry gate artifact. Fill all fields directly in the template before running the validator.

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| document_control | Missing required fields in §1 | Template not fully completed | Fill §1: ID, service name, owner, date; no field may be blank |
| upstream_reference | RR §7 not referenced or the referenced section cannot be found | Handoff from REK not verified before starting | Confirm the RR §7 exists and is complete; do not proceed with SRER until the RR §7 handoff is in place |
| reliability_owner | Reliability owner not named as an individual | Owner left as a team name or role | Name the specific individual who is personally accountable for service reliability |
| initial_slo_baseline | SLO baseline absent, or referenced by a pointer to RR §7 rather than captured directly | Values not copied from RR §7 into SRER | Copy the SLO baseline values from RR §7 directly into the SRER — do not reference by pointer |
| monitoring_confirmed | Monitoring status not confirmed | Monitoring assumed to be live from ORD | Confirm monitoring is live and state the current service health status explicitly |

---

## Service Reliability Profile (SRP-{SERVICE}-{NNN})

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| document_control | Missing version, service name, owner, or SRP ID | Template fields not filled | Complete §1 including version (start at v1.0) and service scope; all fields required |
| slo_definitions | SLO targets aspirational or stated as industry defaults ("99.9% is standard") | Targets not derived from actual service data | Set targets based on the SRER initial baseline; document the data source for each target |
| error_budget | Error budget not calculated from SLO | Budget assumed rather than derived | Calculate explicitly: (1 − SLO target) × measurement window = error budget; document the formula |
| burn_rate_alerts | Alert thresholds absent | Alerting deferred from SRP to implementation | Define burn rate thresholds for fast-burn (short window) and slow-burn (long window) alerts with numeric values |
| measurement_methodology | How SLOs are measured not defined | Methodology assumed from monitoring configuration | State: the measurement source, the query or indicator used, and the measurement window |
| exclusions | Exclusion criteria absent | Exclusions assumed to be obvious | Define what events are excluded from SLO calculation (planned maintenance windows, declared external outages) |
| standard_enforceability | SRP policies written aspirationally | Written as guidance rather than governance | Replace aspirational language with specific, enforceable criteria throughout |

---

## Incident Record (IR-{SERVICE}-{NNN})

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| incident_classification | Severity not from the enumerated list | Free-text severity entered | Use exactly one of: SEV1 / SEV2 / SEV3 / SEV4 |
| impact_statement | Impact described vaguely ("users were affected") | Impact not quantified during or after incident | State: number of users affected, error rate observed, duration of impact, and geographic scope |
| timeline | Timeline has gaps or is absent | Events not recorded during incident | Reconstruct from logs; note gaps explicitly; include detection event, escalation event, and resolution event at minimum |
| root_cause | Root cause stated as "human error" without systemic analysis | Surface cause documented without 5-Why analysis | Include systemic contributing factors: why was the error possible, and what allowed it to propagate without earlier detection |
| remediation | Remediation steps vague or absent | Steps assumed to be obvious from context | Document each remediation action with its timestamp, the actor who performed it, and the observed outcome |
| slo_impact | SLO impact section blank | SLO impact not assessed | Calculate the actual SLO impact using SRP measurement methodology; state the error budget consumed |

---

## Reliability Health Report (RHR-{SERVICE}-{NNN})

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| review_period | Coverage period not defined | Period assumed from context | State the exact start date and end date of the review period |
| slo_compliance | Compliance status not stated | Status assumed from the metric values | State for each SLO: the target, the measured performance, and Pass or Fail compliance status |
| error_budget_state | Budget remaining not calculated | Error budget not tracked across the period | Calculate: budget consumed this period, total remaining, and the trend direction (improving, stable, degrading) |
| incident_summary | Incident section blank when IRs exist for the period | IRs not consulted during RHR generation | Reference each IR in the coverage period by ID; summarize key patterns across incidents |
| layer7_feed | §5 Layer 7 Feed section absent or empty | Feed section treated as optional | Complete §5 with reliability trends and patterns for the IEK; if no patterns were identified, state that explicitly: "No patterns identified this period" |
