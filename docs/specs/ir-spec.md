# Incident Record — Specification

The Incident Record (IR) is the per-incident evidence artifact of the Reliability & Resilience Kit. It documents what happened during an incident from first detection through post-incident review: the classification, impact, timeline of events, root cause analysis, remediation actions, and SLO impact.

The IR is an **evidence artifact** — it documents what occurred; it does not revise the SRP or redefine reliability standards. Every entry traces to observable facts.

---

## Purpose

The Incident Record serves three roles:

1. **Accountability record** — Captures what happened, who responded, and what decisions were made during an incident with enough fidelity for review and audit
2. **Learning artifact** — Documents root cause analysis and follow-up actions in a way that enables systemic improvement over time
3. **Reliability input** — Records the SLO impact of the incident so error budget consumption can be accurately tracked in the RHR

---

## Upstream Dependencies

- Frozen Service Reliability Profile (SRP) — defines the SLOs against which SLO impact is measured
- Incident evidence: detection alerts, monitoring data, responder notes, communication logs

---

## Severity Enumeration

Severity must be selected from this enumerated list. No other values are acceptable.

| Severity | Definition |
|----------|-----------|
| SEV1 | Complete service outage — no users can access the service or core functionality |
| SEV2 | Major degradation — significant percentage of users affected or core functionality severely impaired |
| SEV3 | Partial degradation — subset of users or non-core functionality affected; service remains available |
| SEV4 | Minor issue — minimal user impact; workaround available; not time-critical |

---

## Required Sections

1. Document Control
2. Incident Classification
3. Impact Statement
4. Incident Timeline
5. Root Cause Analysis
6. Remediation
7. SLO Impact

---

## Content Rules

### Document Control
**Rules**
- IR ID must be present (format: IR-{SERVICE}-{NNN})
- SRP ID must be referenced (the frozen SRP active during this incident)
- Incident start time must be present (the time the incident began, as distinct from the time it was detected)
- Incident end time must be present (the time normal operation was confirmed restored)
- Author and review date must be present

**Failure Examples**
- IR ID absent
- SRP ID absent or "latest"
- Start or end time absent
- Author absent

### Incident Classification
**Rules**
- Severity must be selected from the enumerated list (SEV1–SEV4)
- Service must be named
- Detection method must be stated: automated alert | user report | engineer observation | external notification
- Detection timestamp must be present (the time at which the incident was first detected, which may differ from incident start time)

**Failure Examples**
- Severity listed as "high" or "P1" — must be from SEV1–SEV4
- Detection method absent or "monitoring"
- Detection timestamp absent

### Impact Statement
**Rules**
- Users affected must be stated as a count, percentage, or "internal only" — not as a qualitative description
- Affected functionality must be named specifically — not as "the service was down" or "users experienced issues"
- Duration must be stated (from incident start to resolution, in minutes or hours)

**Failure Examples**
- "Some users were affected" — not a count or percentage
- "Core functionality impaired" — not a named functionality
- Duration absent

### Incident Timeline
**Rules**
- Events must be listed in chronological order from first detection to resolution declaration
- Each event must include a timestamp (date and time)
- Each event that involved a named responder must identify that responder by name
- Events must be sufficient to identify: when the incident was detected, when it was declared, key diagnostic and mitigation steps, when mitigation occurred, and when resolution occurred
- Mitigation events and resolution events must be identifiable (may be labeled as such)

**Failure Examples**
- Events not in chronological order
- Timestamps absent from events
- Responders listed as "the on-call team"
- Timeline ends at mitigation without documenting resolution
- No events between detection and resolution

### Root Cause Analysis
**Rules**
- Proximate cause must be identified: the immediate trigger of the incident
- At least one contributing factor must be identified that goes beyond "human error" — e.g., inadequate tooling, missing safeguard, insufficient visibility, unclear runbook
- If the proximate cause was human action, the contributing factor must explain why the system permitted or did not prevent the human error
- Systemic factors must be assessed — may be "none identified" but must not be blank; "none identified" requires a brief explanation of why
- Root cause analysis must reflect what was known at time of post-incident review, not speculative future analysis

**Failure Examples**
- "Human error" as root cause with no further analysis
- Contributing factors absent
- Systemic factors section blank
- Root cause stated as "unknown" without explanation of investigation steps taken

### Remediation
**Rules**
- Immediate fix must be documented: what was done to stop the incident and restore service
- Confirmation of restoration must be present: how was it confirmed that normal operation was restored (metric values, alert resolution, or manual verification)
- At least one follow-up action must be present with: owner (named individual), deadline (specific date), and tracking reference (ticket ID, task system reference, or equivalent)
- Actions without all three fields (owner, deadline, tracking reference) do not satisfy this requirement

**Failure Examples**
- "We restarted the service" — immediate fix documented but confirmation absent
- No follow-up actions listed
- Follow-up actions listed without owner, deadline, or tracking reference
- Tracking reference listed as "TBD"

### SLO Impact
**Rules**
- Each SLO in the active SRP that was affected by this incident must be identified
- For each affected SLO, the error budget consumed must be stated as: minutes (or equivalent unit) and percentage of the measurement window budget
- Remaining error budget post-incident must be stated for each affected SLO
- Policy implication must be stated per the SRP §3 consumption policy: does the remaining budget trigger any threshold-defined action?
- If no SLOs were affected (e.g., a SEV4 with impact below SLO measurement threshold), this must be explicitly stated with reasoning — not left blank

**Failure Examples**
- Affected SLOs not identified
- Budget consumption stated as percentage only without time equivalent
- Remaining budget absent
- Policy implication absent
- Section blank for a non-trivial incident

---

## Format Requirements

- All timestamps must include date and time (not date only)
- Impact counts must be specific (numbers, percentages, or "internal only")
- Duration must be in clock time (minutes or hours) — not "brief" or "extended"
- SLO budget consumption must include units consistent with the SRP measurement window
- All responder names must be individual names, not team names

---

## Completeness Rules

- All seven sections must be present and non-empty
- Severity from enumerated list
- At least one contributing factor in root cause analysis
- At least one follow-up action with owner, deadline, and tracking reference
- SLO impact assessed for all affected SLOs (or "none affected" with reasoning)

---

## Relationship Rules

- The IR references the SRP version active during the incident — if the SRP is revised after the incident, the IR is not retroactively updated
- The IR is frozen after post-incident review is complete and follow-up actions are documented — it does not need to remain open until actions are closed
- RHRs summarize IR data; they do not modify IR conclusions
- A frozen IR is immutable — corrections require issuing a new IR version (IR-{SERVICE}-{NNN}v2) with a note referencing the original

---

## Hard Gates

1. **incident_classification** — Severity from enumerated list (SEV1–SEV4); service named; detection method from enumerated list; detection timestamp present
2. **impact_statement** — Users affected as count, percentage, or "internal only"; affected functionality named specifically; duration in clock time
3. **timeline** — Chronological events from detection to resolution; each timestamped; named responders where actions were taken; mitigation and resolution identifiable
4. **root_cause** — Proximate cause identified; at least one contributing factor beyond "human error"; systemic factors assessed (may be "none identified" — not blank)
5. **remediation** — Immediate fix documented with confirmation of restoration; at least one follow-up action with named owner, specific deadline, and tracking reference
6. **slo_impact** — Affected SLOs identified; budget consumed in minutes or percentage; remaining budget stated; policy implication per SRP §3 stated; if no SLOs affected, stated explicitly with reasoning
