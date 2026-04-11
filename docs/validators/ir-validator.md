# Incident Record — Validator

This validator evaluates a completed Incident Record (IR) against `docs/specs/ir-spec.md`. It is used in a separate AI session from the one that generated the IR.

**Validator role:** Judge pass/fail. Do not suggest improvements, redesign content, or offer alternatives. Evaluate only what is explicitly present.

---

## Inputs Required

To run this validator, provide:
1. The completed IR (full document)
2. `docs/specs/ir-spec.md` (the spec — use this as the authoritative rules)

Do not use any other document as the source of truth for pass/fail criteria.

---

## Evaluation Procedure

Evaluate each hard gate in order. For each gate, apply the rules exactly as stated in the spec. Do not infer intent. Do not give partial credit. Ambiguity in the artifact is a failure condition — if you cannot determine whether a gate passes from what is explicitly present, the gate fails.

---

## Hard Gate Checks

### Gate 1: incident_classification

Check:
- Severity is from the enumerated list: SEV1 | SEV2 | SEV3 | SEV4
  - Severities expressed as "P1," "Critical," "High," or other non-enumerated values fail this gate
- Service is named
- Detection method is from the enumerated list: automated alert | user report | engineer observation | external notification
  - "monitoring" alone, "alert," or other paraphrases are not from the enumerated list and fail
- Detection timestamp is present with date and time (not date only)

**Pass:** All four fields present with required enumerated values.
**Fail:** Severity not from SEV1–SEV4; service absent; detection method not from enumerated list; detection timestamp absent or date-only.

---

### Gate 2: impact_statement

Check:
- Users affected is a count, percentage, or the exact phrase "internal only" — qualitative descriptions ("some users," "many users," "widespread impact") fail
- Affected functionality is specifically named — "the service was down," "core functionality," or "users experienced issues" fail
- Duration is stated in clock time (minutes or hours with a number) — "brief," "extended," or "several hours" without a number fail

**Pass:** All three fields present with required specificity.
**Fail:** Users affected stated qualitatively; affected functionality named generically; duration absent or qualitative.

---

### Gate 3: timeline

Check:
- Events are listed in chronological order
- Each event has a timestamp with date and time
- At least one event names a specific individual as responder (not "the team," "on-call," or equivalent)
- The timeline is sufficient to identify: detection, declaration, at least one diagnostic or mitigation step, and resolution
- Mitigation and resolution are both identifiable in the timeline (labeled, or clearly discernible from event descriptions)

**Pass:** Chronological order; timestamps present; at least one named responder; detection, mitigation, and resolution events present.
**Fail:** Events not in order; timestamps absent; no named responders in any event; timeline jumps from detection to resolution with no intermediate events; resolution not identifiable.

---

### Gate 4: root_cause

Check:
- A proximate cause is stated (the immediate trigger — what happened to start the incident)
- At least one contributing factor is present that is not "human error" alone — must identify a systemic condition, tool gap, process gap, or structural factor
- If the proximate cause was human action, a contributing factor must explain why the system did not prevent or catch the human error
- Systemic factors are assessed — may be "none identified" with explanation, but may not be blank

**Pass:** Proximate cause present; at least one contributing factor beyond pure human error; systemic factors assessed (not blank).
**Fail:** Proximate cause absent; only "human error" with no further analysis; no contributing factors; systemic factors section blank.

---

### Gate 5: remediation

Check:
- Immediate fix is documented (what action was taken to stop the incident)
- Confirmation of restoration is present (how it was verified — metric value, alert resolution, or manual check with result)
- At least one follow-up action is present with:
  - Named owner (individual, not team)
  - Specific deadline date (YYYY-MM-DD or equivalent — "next sprint" fails)
  - Tracking reference (ticket ID, task system reference — "TBD" fails)
- All three fields must be present for at least one action; actions missing any of the three fields do not satisfy this requirement

**Pass:** Immediate fix documented; restoration confirmed; at least one action with named owner, specific date, and valid tracking reference.
**Fail:** Immediate fix absent; restoration confirmation absent; no follow-up actions; all actions missing one or more of owner/deadline/tracking reference; tracking reference is "TBD."

---

### Gate 6: slo_impact

Check:
- Affected SLOs are identified (SLO names from the active SRP that were impacted)
- For each affected SLO:
  - Budget consumed is stated in both a time unit (minutes or equivalent) and a percentage of the measurement window budget
  - Remaining budget post-incident is stated
  - Policy implication is stated per the SRP consumption policy
- If no SLOs were affected: this must be explicitly stated with reasoning (not blank)

**Pass:** Affected SLOs identified with consumption in time and percentage and remaining budget; policy implication stated; or "none affected" with reasoning.
**Fail:** Section blank; affected SLOs not identified; budget consumption stated as percentage only without time; remaining budget absent; policy implication absent; unaffected conclusion without reasoning.

---

## Output Format

Produce a JSON result in exactly this format:

```json
{
  "status": "PASS | FAIL",
  "summary": "<one sentence verdict>",
  "hard_gates": {
    "incident_classification": "PASS | FAIL",
    "impact_statement": "PASS | FAIL",
    "timeline": "PASS | FAIL",
    "root_cause": "PASS | FAIL",
    "remediation": "PASS | FAIL",
    "slo_impact": "PASS | FAIL"
  },
  "blocking_issues": [
    {
      "gate": "<gate_name>",
      "description": "<what specifically failed>",
      "location": "<section or field where the failure is>"
    }
  ],
  "warnings": [
    {
      "description": "<non-blocking observation>",
      "location": "<section>"
    }
  ],
  "completeness_score": "<0-100>"
}
```

**Interpretation rules:**
- Any gate failure → `"status": "FAIL"`
- `blocking_issues` lists exactly the failures — no additional content
- `warnings` are non-blocking; they do not affect status
- `completeness_score` is advisory; it does not override gate results
- If all gates pass, `blocking_issues` is an empty array

---

## Validator Constraints

- Do not suggest how to fix failures
- Do not redesign the incident timeline or root cause analysis
- Do not evaluate writing quality beyond spec requirements
- Do not infer severity from impact description if the severity field is missing or non-enumerated
- Evaluate only what is explicitly present in the document
