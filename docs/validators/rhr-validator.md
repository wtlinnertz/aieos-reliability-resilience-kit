# Reliability Health Report — Validator

This validator evaluates a completed Reliability Health Report (RHR) against `docs/specs/rhr-spec.md`. It is used in a separate AI session from the one that generated the RHR.

**Validator role:** Judge pass/fail. Do not suggest improvements, redesign content, or offer alternatives. Evaluate only what is explicitly present.

---

## Inputs Required

To run this validator, provide:
1. The completed RHR (full document)
2. `docs/specs/rhr-spec.md` (the spec — use this as the authoritative rules)
3. The active SRP (to verify that all SLOs are reported)

Do not use any other document as the source of truth for pass/fail criteria.

---

## Evaluation Procedure

Evaluate each hard gate in order. For each gate, apply the rules exactly as stated in the spec. Do not infer intent. Do not give partial credit. Ambiguity in the artifact is a failure condition — if you cannot determine whether a gate passes from what is explicitly present, the gate fails.

---

## Hard Gate Checks

### Gate 1: review_period

Check:
- RHR ID is present and matches the format `RHR-{SERVICE}-{NNN}`
- Coverage period has specific start and end dates (calendar dates, not quarter names or relative references)
- Service name is present
- Review owner is present (individual or role — either is acceptable for this gate)
- SRP ID is referenced and its status is confirmed as Frozen
- If multiple SRP versions were active, all versions are referenced with their sub-period dates

**Pass:** All fields present; specific dates; SRP referenced as Frozen; multiple versions referenced if applicable.
**Fail:** RHR ID absent; coverage period as "Q1" or relative ("last month"); service absent; review owner absent; SRP absent or status not confirmed as Frozen; multiple SRP versions active but only one referenced.

---

### Gate 2: slo_compliance

Check:
- Every SLO defined in the referenced SRP is reported — cross-reference the SRP to confirm none are omitted
- For each SLO:
  - Target percentage is stated (must match the SRP target)
  - Actual compliance percentage is stated as a number (not a range, not "within target")
  - Met/Missed verdict is explicitly stated
- Measurement data gaps are documented with dates, reason, and duration — or the explicit statement that no gaps occurred
- Compliance percentages are stated as monitoring data, not as assertions

**Pass:** All SRP SLOs reported; all three fields per SLO; gaps documented or absence confirmed.
**Fail:** Any SRP SLO not reported; any SLO missing target, actual percentage, or verdict; verdict stated as "approximately met" or similar; measurement gaps not addressed; compliance stated as assertion ("the service met its SLO").

---

### Gate 3: error_budget_state

Check:
- Remaining budget is stated for each SLO in both percentage and time equivalent (with units)
- Burn rate over the coverage period is stated for each SLO (a number — average is acceptable)
- Policy implication per SRP §3 is stated for each SLO (what threshold it corresponds to and what action it requires)
- If the SRP freeze/slowdown condition was crossed during the period, it is noted with the date

**Pass:** Remaining budget in both forms for all SLOs; burn rate for all SLOs; policy implication for all SLOs; freeze crossings documented or absence confirmed.
**Fail:** Remaining budget as percentage only (no time equivalent); time equivalent missing units; burn rate absent; policy implication absent or stated as "within normal range" without referencing SRP §3 thresholds; freeze condition crossing known but not documented.

---

### Gate 4: incident_summary

Check:
- Total incident count is stated
- Count by severity is present for all four levels (SEV1 through SEV4); missing severity levels must be shown as zero, not omitted
- Total user-impact duration is stated in clock time with a number and unit — "several hours" or "minimal" fails
- Pattern observation is present — may be "no patterns identified" with brief explanation; may not be blank
- Each IR is identified by IR ID (or "no incidents occurred" is stated)

**Pass:** Total count; all four severity levels; numeric user-impact duration; pattern observation present; IRs identified by ID.
**Fail:** Count absent; any severity level missing (even if zero); user-impact duration qualitative; pattern observation blank; IRs not identified by ID.

---

### Gate 5: layer7_feed

Check:
- Reliability trend is stated as one of: improving | stable | degrading
- A brief basis for the trend is present (a reason, not just the label)
- Systemic issues are assessed — may be "none identified" with explanation; may not be blank
- Improvement signals are stated — may be "none identified"
- Recommended continued watch items are present — may be "none" if stable; may not be absent

**Pass:** Trend with basis; systemic issues assessed; improvement signals present; watch items present.
**Fail:** Trend absent; trend present without basis; systemic issues blank; recommended watch items absent.

---

## Output Format

Produce a JSON result in exactly this format:

```json
{
  "status": "PASS | FAIL",
  "summary": "<one sentence verdict>",
  "hard_gates": {
    "review_period": "PASS | FAIL",
    "slo_compliance": "PASS | FAIL",
    "error_budget_state": "PASS | FAIL",
    "incident_summary": "PASS | FAIL",
    "layer7_feed": "PASS | FAIL"
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
- Do not redesign the SLO compliance table or Layer 7 narrative
- Do not evaluate writing quality beyond spec requirements
- Do not accept qualitative compliance assessments as equivalent to numeric data
- Cross-reference the active SRP to verify all SLOs are reported — this is a required step
- Evaluate only what is explicitly present in the document
