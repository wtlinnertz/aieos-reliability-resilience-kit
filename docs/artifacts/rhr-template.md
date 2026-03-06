# Reliability Health Report

---

## 1. Document Control

| Field | Value |
|-------|-------|
| RHR ID | RHR-{SERVICE}-{NNN} |
| Service | {service name} |
| Coverage Period | {YYYY-MM-DD} through {YYYY-MM-DD} |
| Review Owner | {individual or role} |
| SRP Reference | SRP-{SERVICE}-{NNN} (version: v{N}, status: Frozen) |
| Status | Draft / Validated / Freeze Pending / Frozen |
| Governance Model Version | 1.0 |
| Prompt Version | {prompt version} |

*If multiple SRP versions were active during the coverage period, list all:*
*SRP versions: v{N} ({start date} – {end date}), v{N+1} ({start date} – {end date})*

---

## 2. SLO Compliance

| SLO Name | Target | Actual Compliance | Met / Missed |
|----------|--------|------------------|-------------|
| {SLO 1 name} | {X.X%} | {X.XX%} | {Met \| Missed} |
| {SLO 2 name} | {X.X%} | {X.XX%} | {Met \| Missed} |
| {SLO 3 name} | {X.X%} | {X.XX%} | {Met \| Missed} |

**Measurement Data Gaps:**

| Period | Reason | Duration | Effect on Calculation |
|--------|--------|----------|----------------------|
| {YYYY-MM-DD to YYYY-MM-DD} | {reason} | {N hours/days} | {how this affects the reported compliance percentage} |

*If no measurement gaps, write: "No measurement data gaps during this coverage period."*

---

## 3. Error Budget State

| SLO Name | Budget at Period Start | Consumed | Remaining | Avg Burn Rate | Policy Implication |
|----------|----------------------|----------|-----------|--------------|-------------------|
| {SLO 1 name} | {X.X%} ({N min}) | {X.X%} ({N min}) | {X.X%} ({N min}) | {N}× | {SRP §3 threshold status and required action} |
| {SLO 2 name} | {X.X%} ({N min}) | {X.X%} ({N min}) | {X.X%} ({N min}) | {N}× | {SRP §3 threshold status and required action} |
| {SLO 3 name} | {X.X%} ({N min}) | {X.X%} ({N min}) | {X.X%} ({N min}) | {N}× | {SRP §3 threshold status and required action} |

**Freeze Condition Events:**
{List any dates during the coverage period when the SRP freeze/slowdown condition was crossed, or "No freeze condition crossed during this coverage period."}

---

## 4. Incident Summary

**Total incidents during coverage period:** {N}

| Severity | Count |
|----------|-------|
| SEV1 | {N} |
| SEV2 | {N} |
| SEV3 | {N} |
| SEV4 | {N} |
| **Total** | **{N}** |

**Total user-impact duration:** {N minutes / N hours} (sum of durations for user-impacting incidents)

**Incidents referenced:**
- {IR-{SERVICE}-{NNN}} — {brief description, date}
- {IR-{SERVICE}-{NNN}} — {brief description, date}

*If no incidents occurred: "No incidents during this coverage period." Severity table must still show all zeros.*

**Pattern Observation:**
{A statement about incident patterns, recurring root causes, trends, or distribution. Must not be blank. If no patterns were observed, state: "No patterns identified: [brief explanation — e.g., only one incident occurred, or no shared root causes across incidents]."}

---

## 5. Handoff to Layer 7

**Reliability Trend:**
{improving | stable | degrading} — {brief basis: e.g., "SLO compliance improving 0.2% month-over-month across last 3 periods" or "first reporting period; trend not yet established"}

**Systemic Issues:**
{Outstanding systemic issues identified in IRs during the period — actions not yet resolved, recurring patterns, or structural risks. Or: "None identified: [brief explanation]."}

**Improvement Signals:**
{Positive developments in reliability during the period — new alerting, reduced MTTR, runbook improvements, fixed instrumentation gaps. Or: "None identified during this period."}

**Recommended Continued Watch Items:**
{Aspects of the service that merit elevated attention in the next reporting period. Or: "None — service appears stable across all SLOs."}
