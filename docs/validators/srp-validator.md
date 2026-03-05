# Service Reliability Profile — Validator

This validator evaluates a completed Service Reliability Profile (SRP) against `docs/specs/srp-spec.md`. It is used in a separate AI session from the one that generated the SRP.

**Validator role:** Judge pass/fail. Do not suggest improvements, redesign content, or offer alternatives. Evaluate only what is explicitly present.

---

## Inputs Required

To run this validator, provide:
1. The completed SRP (full document)
2. `docs/specs/srp-spec.md` (the spec — use this as the authoritative rules)

Do not use any other document as the source of truth for pass/fail criteria.

---

## Evaluation Procedure

Evaluate each hard gate in order. For each gate, apply the rules exactly as stated in the spec. Do not infer intent. Do not give partial credit. Ambiguity in the artifact is a failure condition — if you cannot determine whether a gate passes from what is explicitly present, the gate fails.

---

## Hard Gate Checks

### Gate 1: document_control

Check:
- SRP ID is present and matches the format `SRP-{SERVICE}-{NNN}`
- Service name is present
- Owner is a team or role — not an individual person's name
- Version is present (format: v{N}, e.g., v1)
- Status is one of: Draft | Validated | Frozen
- Scope is stated (which deployment or environment this SRP governs — not absent, not "all environments")

**Pass:** All six fields present and non-empty with required format/content.
**Fail:** Any field absent; owner is an individual person's name; version absent or in wrong format; status not from enumerated list; scope absent or "all."

---

### Gate 2: slo_definitions

Check:
- At least 2 SLOs are defined
- For each SLO:
  - A name (human-readable label) is present and unique within this SRP
  - A named SLI metric is present (not just described — must be a metric name or specific behavior)
  - A target percentage is present with at least one decimal place
  - A measurement window is present (not just "monthly" — must specify rolling/calendar)
  - A basis for the target is stated — one of: historical data with reference, contractual requirement with citation, first-release baseline with reference, user research finding; "industry standard" without reference fails

**Pass:** At least 2 SLOs, all four fields present for each.
**Fail:** Fewer than 2 SLOs; any SLO missing name, metric, target, window, or basis; duplicate SLO names; basis stated as "standard" or "industry norm" without reference.

---

### Gate 3: error_budget

Check:
- Error budget is calculated for each SLO defined in §2
- Each budget is expressed as a percentage (100% − target%)
- Each budget is expressed as a time equivalent (minutes or hours per measurement window) with units
- A consumption policy is present with at least one specific threshold and a named action
- A freeze/slowdown condition is stated with a specific budget consumption percentage and a named consequence

**Pass:** Budget calculated for all SLOs in both percentage and time equivalent; consumption policy with threshold and action present; freeze condition with specific percentage and named consequence present.
**Fail:** Budget calculated for some but not all SLOs; time equivalent absent or missing units; consumption policy absent or qualitative only ("when budget is low"); freeze condition absent or qualitative.

---

### Gate 4: burn_rate_alerts

Check:
- A fast burn alert is defined with a numeric multiplier threshold, a window duration, and an approximate budget consumption basis
- A slow burn alert is defined with a numeric multiplier threshold, a window duration, and an approximate budget consumption basis
- Alert routing is defined for both alerts (specific channel, pager, or escalation path — not "default alerts")
- Alert thresholds are stated as numeric values — qualitative descriptions ("high," "elevated") fail

**Pass:** Both alerts present with numeric thresholds, windows, consumption basis, and routing.
**Fail:** Only one alert defined; either alert missing threshold, window, or consumption basis; either alert missing routing; routing stated as "default alerts"; thresholds described qualitatively.

---

### Gate 5: measurement_methodology

Check:
- A measurement methodology entry exists for every SLI defined in §2
- Each entry has an exact metric name (an exact string, not a description — "error rate" fails; `service_request_errors_total` passes)
- Each entry has a data source (monitoring system, service, instrumentation point)
- Each entry has a calculation method
- If any SLI is not yet instrumented, it is documented as a gap with a target instrumentation date (not left blank or described as "pending")

**Pass:** Every SLI from §2 has methodology; all three fields present for each; gaps documented with target dates.
**Fail:** Any SLI from §2 missing a methodology entry; any entry with a described metric name instead of an exact name; data source absent; calculation method absent; uninstrumented SLIs not documented as gaps.

---

### Gate 6: exclusions

Check:
- Maintenance window policy is stated (does or does not exclude from SLO measurement — either is acceptable, but the policy must be stated)
- Planned downtime handling is stated (does or does not exclude from error budget — either is acceptable)
- If exclusions apply, the exclusion declaration process is stated (advance notice requirement, who may declare)
- Section is not blank and not "TBD"

**Pass:** Both policies stated; declaration process stated if exclusions apply.
**Fail:** Section absent or blank; either policy absent; "TBD"; exclusions apply but declaration process not stated.

---

### Gate 7: standard_enforceability

Check: Review every standard in the SRP for aspirational language.
- Fail conditions: "should," "ideally," "when possible," "as needed," "where feasible," "typically," or equivalent qualifiers that allow the standard to be bypassed without violating it
- Pass conditions: All thresholds are numeric; all conditions are specific; no exceptions phrased as preferences

**Pass:** No aspirational language found in any standard.
**Fail:** Any standard uses aspirational language that prevents objective compliance evaluation.

---

## Output Format

Produce a JSON result in exactly this format:

```json
{
  "status": "PASS | FAIL",
  "summary": "<one sentence verdict>",
  "hard_gates": {
    "document_control": "PASS | FAIL",
    "slo_definitions": "PASS | FAIL",
    "error_budget": "PASS | FAIL",
    "burn_rate_alerts": "PASS | FAIL",
    "measurement_methodology": "PASS | FAIL",
    "exclusions": "PASS | FAIL",
    "standard_enforceability": "PASS | FAIL"
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
- Do not redesign or improve the SRP
- Do not evaluate writing quality beyond spec requirements
- Do not accept paraphrased metric names as equivalent to exact names
- Evaluate only what is explicitly present in the document
