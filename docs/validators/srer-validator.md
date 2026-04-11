# Service Reliability Entry Record — Validator

This validator evaluates a completed Service Reliability Entry Record (SRER) against `docs/specs/srer-spec.md`. It is used in a separate AI session from the one that completed the SRER.

**Validator role:** Judge pass/fail. Do not suggest improvements, redesign content, or offer alternatives. Evaluate only what is explicitly present.

---

## Inputs Required

To run this validator, provide:
1. The completed SRER (full document)
2. `docs/specs/srer-spec.md` (the spec — use this as the authoritative rules)

Do not use any other document as the source of truth for pass/fail criteria.

---

## Evaluation Procedure

Evaluate each hard gate in order. For each gate, apply the rules exactly as stated in the spec. Do not infer intent. Do not give partial credit. Ambiguity in the artifact is a failure condition — if you cannot determine whether a gate passes from what is explicitly present, the gate fails.

---

## Hard Gate Checks

### Gate 1: document_control

Check:
- SRER ID is present and matches the format `SRER-{PROJECT}-{NNN}`
- Date is present (any standard date format is acceptable)
- Service summary is present and contains at least 1–2 sentences (not blank, not "TBD")

**Pass:** All three fields present and non-empty with required content.
**Fail:** Any field absent, blank, or "TBD."

---

### Gate 2: upstream_reference

Check:
- An RR ID is present and matches the format `RR-{PROJECT}-{NNN}` (or similar structured ID)
- RR status is stated as **Frozen** (not Draft, not Validated, not Approved)
- The following RR §7 fields are captured in the SRER body (not merely a pointer to the RR):
  - Production state (system name AND version or commit AND deployed configuration)
  - Current exposure state
  - Monitoring active (list of monitoring items)
  - SLO baseline (values with source identified)
  - Open incidents at handoff (may be "None" — must be present)
  - Recommended watch items

**Pass:** All six sub-checks pass.
**Fail:** RR ID absent; RR status not Frozen; any RR §7 field missing from the SRER body; RR §7 represented only as "see RR document."

---

### Gate 3: reliability_owner

Check:
- A named individual is present (a person's name — not a team name, not a role title like "Platform Team" or "On-Call Engineer")
- On-call contact information is present (channel, pager, or equivalent)
- Scope is stated (which service or component this person owns)

**Pass:** All three fields present for a named individual.
**Fail:** "Team" or role title listed instead of a person's name; on-call contact absent; scope absent.

---

### Gate 4: initial_slo_baseline

Check:
- SLO baseline values are present (at least one SLO with metric name, value, and window)
- Each SLO entry has a source identified — a document ID or explicit reference (not a description like "from the ORD")
- No SLO value is invented: "99.9% is standard," "TBD," or similar without reference fails this gate
- Values are consistent with what is captured in the upstream reference section (§2 SLO baseline)

**Pass:** At least one SLO with metric name, baseline value, measurement window, and source document ID.
**Fail:** No SLO values present; values present without source; values stated as "standard" or "TBD"; source described rather than identified by document ID.

---

### Gate 5: monitoring_confirmed

Check:
- Confirmation date is present
- At least one monitoring item from RR §7 is listed with its alert threshold
- Alert thresholds are stated as numeric values (not qualitative descriptions like "standard threshold")
- If monitoring gaps exist: each gap has a named resolution owner and a target date
- If the section states "all monitoring active" or equivalent, verify that at least the items listed in §2 (monitoring active) are accounted for

**Pass:** Confirmation date present; at least one item with numeric threshold; gaps documented with owner and date if any exist.
**Fail:** Confirmation date absent; no items with numeric thresholds; gaps acknowledged but not given owner or target date; section empty or a pointer to RR §7.

---

## Output Format

Produce a JSON result in exactly this format:

```json
{
  "status": "PASS | FAIL",
  "summary": "<one sentence verdict>",
  "hard_gates": {
    "document_control": "PASS | FAIL",
    "upstream_reference": "PASS | FAIL",
    "reliability_owner": "PASS | FAIL",
    "initial_slo_baseline": "PASS | FAIL",
    "monitoring_confirmed": "PASS | FAIL"
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
- Do not redesign or improve the SRER
- Do not evaluate content quality beyond what the spec requires
- Do not accept inferred information as equivalent to explicit content
- Evaluate only what is present in the document
