# Reliability Health Report — Specification

Version: v1.0

The Reliability Health Report (RHR) is the periodic review artifact of the Reliability & Resilience Kit. It synthesizes SLO compliance data, error budget state, and incident history over a defined coverage period into a structured reliability picture — and produces the Layer 7 feed that downstream insight and evolution systems consume.

The RHR is a **synthesis artifact** — it does not revise SLOs, reopen incidents, or introduce new reliability standards. It reports what happened during the coverage period as measured against the frozen SRP.

---

## What This Artifact Is Not

- **Not a reliability retrospective or postmortem.** The RHR synthesizes the coverage period's reliability data into a structured picture. Root cause analysis for individual incidents belongs in the IR or ODK PMR.
- **Not an SRP revision.** If the RHR reveals that SLO targets need to change, the SRP revision process is triggered — the RHR documents the signal, not the new targets.
- **Not a project plan.** The RHR produces a Layer 7 feed with reliability signals and recommended actions; it does not assign tasks, owners, or delivery dates. Action planning belongs downstream.

---

## Purpose

The Reliability Health Report serves three roles:

1. **Compliance record** — Documents whether each SLO was met during the coverage period, based on actual monitoring data
2. **Budget state report** — States the remaining error budget per SLO and identifies whether any SRP-defined thresholds have been crossed
3. **Layer 7 feed** — Provides the reliability trend, systemic issues, improvement signals, and recommended watch items for downstream consumption

---

## Upstream Dependencies

- Frozen Service Reliability Profile (SRP) — defines the SLOs against which compliance is measured; must be the version(s) active during the coverage period
- All Incident Records (IRs) from the coverage period — frozen, validated
- Monitoring data covering the review period

---

## Required Sections

1. Document Control
2. SLO Compliance
3. Error Budget State
4. Incident Summary
5. Handoff to Layer 7

---

## Content Rules

### Document Control
**Rules**
- RHR ID must be present (format: RHR-{SERVICE}-{NNN})
- Coverage period start and end dates must be present
- Service must be named
- Review owner must be named (individual or role — either is acceptable for RHR)
- SRP ID must be referenced; SRP must be in Frozen status
- If multiple SRP versions were active during the coverage period, all versions must be referenced

**Failure Examples**
- RHR ID absent
- Coverage period absent or "Q1" without specific dates
- SRP ID absent or "latest"
- SRP status not confirmed as Frozen

### SLO Compliance
**Rules**
- Every SLO defined in the active SRP must be reported — none may be omitted
- For each SLO:
  - Target percentage must be stated (from SRP)
  - Actual compliance percentage during the coverage period must be stated
  - Met/missed verdict must be stated explicitly (Met | Missed)
- Any periods during the coverage period where measurement data was unavailable must be documented (dates, reason, duration)
- If measurement data gaps are present, the compliance percentage must note the gap and its effect on the calculation
- Compliance percentages must be based on monitoring data — assertions ("the service met its SLO") without data are not acceptable

**Failure Examples**
- An SLO from the SRP not reported
- Actual compliance percentage absent
- Met/missed verdict absent
- Measurement gaps not documented
- Compliance stated as "within target" without a percentage

### Error Budget State
**Rules**
- Remaining budget must be stated per SLO as both percentage and time equivalent at end of coverage period
- Burn rate over the coverage period must be stated per SLO (average burn rate is acceptable)
- Policy implication per SRP §3 must be stated for each SLO: which SRP-defined threshold does the remaining budget correspond to, and what action does that threshold require?
- If the SRP freeze/slowdown condition was crossed during the coverage period, it must be noted with the date it was crossed

**Failure Examples**
- Remaining budget stated as percentage only without time equivalent
- Burn rate absent
- Policy implication absent or "within normal range" without reference to SRP §3 thresholds
- Freeze condition crossing undocumented

### Incident Summary
**Rules**
- Total incident count must be stated
- Count by severity (SEV1 through SEV4) must be stated; missing severity levels must be shown as zero, not omitted
- Total user-impact duration must be stated in clock time (sum of incident durations for incidents with user impact)
- At least one pattern observation must be present: a statement about incident patterns, recurring root causes, trends, or distribution — or the explicit statement "no patterns identified" with a brief explanation; this section may not be blank
- Each IR referenced in this summary must be identified by IR ID

**Failure Examples**
- Count by severity absent
- Total user-impact duration absent or "several hours"
- Pattern observation absent (section blank is a failure)
- IRs not identified by ID

### Handoff to Layer 7
**Rules**
- Reliability trend must be stated: improving | stable | degrading, with a brief basis (e.g., "SLO compliance improving 0.2% month-over-month over 3 periods")
- Systemic issues must be assessed: outstanding systemic issues identified in IRs during the period — or the explicit statement "none identified" with reasoning; this field may not be blank
- Improvement signals must be stated: positive developments in reliability during the period (new alerting, improved runbooks, reduced MTTR) — or "none identified"
- Recommended continued watch items must be listed: aspects of the service that merit elevated attention in the next period — or "none" if the service is stable; this must not be blank

**Failure Examples**
- Reliability trend absent or stated without basis
- Systemic issues section blank
- Recommended watch items absent

---

## Format Requirements

- Coverage period dates must be specific calendar dates (e.g., "2026-01-01 through 2026-01-31")
- SLO compliance percentages must include at least two decimal places (e.g., 99.94%)
- Error budget time equivalents must include units consistent with the SRP measurement window
- Incident count table must include all four severity levels

---

## Completeness Rules

- All five sections must be present and non-empty
- Every SRP SLO reported in compliance section
- Error budget state calculated for each SLO
- Incident summary includes all IRs from the coverage period
- Layer 7 feed includes all four required fields

---

## Relationship Rules

- The RHR reports against the SRP — it does not revise SLOs or error budget policy
- If SRP was revised during the coverage period, compliance must be reported separately for each SRP version's sub-period
- IRs are input to the RHR; the RHR does not modify IR conclusions
- A frozen RHR is the Layer 7 feed for the coverage period — it is not amended post-freeze; corrections require a new RHR version

---

## Hard Gates

1. **review_period** — RHR ID, coverage period start/end dates, service name, review owner, and SRP ID (Frozen) present; multiple SRP versions referenced if applicable
2. **slo_compliance** — Every SRP SLO reported with target, actual compliance %, and Met/Missed verdict; measurement data gaps documented; compliance based on monitoring data not assertions
3. **error_budget_state** — Remaining budget per SLO in percentage and time equivalent; burn rate over period; policy implication per SRP §3 stated; freeze condition crossings noted
4. **incident_summary** — Count and severity breakdown (all four levels); total user-impact duration in clock time; pattern observation present (or "no patterns identified" — not blank); IRs identified by ID
5. **layer7_feed** — Reliability trend with basis; systemic issues assessed (or "none identified" — not blank); improvement signals; recommended continued watch items (or "none" — not blank)
