# SRP → RHR Consistency Check — Utility Prompt

## Purpose

Verify that the Reliability Health Report (RHR) is internally consistent with the Service Reliability Profile (SRP) it references. This check confirms that the RHR's SLO targets, error budget calculations, and SRP version citation are accurate before the RHR is validated and frozen.

This is a **utility prompt** — it produces analysis for human review. Its output does not substitute for the RHR Validator, which evaluates the RHR against its spec.

## When to Use

Run this prompt after the RHR has been drafted and before running the RHR Validator — to catch SLO target mismatches, calculation errors, and stale SRP version references before they cause a validator FAIL.

Especially useful when:
- The SRP has been revised during the coverage period (multiple SRP versions active)
- The RHR is the first report for this service
- Error budget calculations are complex or span multiple SLOs

## Inputs Required

1. The frozen SRP (all relevant versions if multiple were active during the coverage period)
2. The drafted RHR

## Instructions

### Step 1: SRP Version Citation Check

The RHR §1 Document Control must cite the correct SRP version(s):

1. Confirm the SRP ID and version number(s) in the RHR Document Control match the SRP(s) provided.
2. If multiple SRP versions were active during the coverage period, confirm the RHR either cites all of them or explicitly notes the version boundary dates.
3. Flag if the RHR cites an SRP version that does not exist or is not frozen.

### Step 2: SLO Target Consistency (§2 vs SRP §2)

Compare each SLO in the RHR §2 against the corresponding SLO in SRP §2:

1. For each SLO row in the RHR compliance table:
   - Confirm the SLO name matches the SRP §2 definition.
   - Confirm the target percentage matches the SRP §2 target exactly.
   - Confirm the measurement window matches the SRP §2 measurement window.
2. Flag any RHR SLO that uses a different target or window than the SRP defines.
3. Flag any SLO defined in the SRP §2 that is absent from the RHR §2 compliance table (silently dropped SLO).

### Step 3: Error Budget Calculation Consistency (§3 vs SRP §3)

Compare the RHR §3 error budget calculations against SRP §3 error budget policy:

1. Confirm the budget allocation used in each row of the RHR §3 table is derivable from the SRP §3 error budget definitions.
2. Check arithmetic: for each SLO, verify that `Budget at Period Start` + `Consumed` = measurement window budget, and that `Remaining` = `Budget at Period Start` - `Consumed`.
3. Confirm the `Policy Implication` column in the RHR §3 correctly identifies which SRP §3 threshold applies based on the remaining budget.
4. If the coverage period spans multiple SRP versions with different error budget policies, confirm the RHR correctly applies the appropriate policy for each sub-period.

### Step 4: Freeze Condition Accuracy

Cross-reference the RHR §3 Freeze Condition Events against the SRP §3 freeze/slowdown threshold:

1. Confirm the freeze condition threshold cited or implied in the RHR matches the SRP §3 definition.
2. Confirm that any freeze condition events listed in the RHR correspond to periods when budget consumption actually crossed the SRP §3 threshold.
3. If the RHR states "No freeze condition crossed," confirm this is consistent with the remaining budget figures in §3.

### Step 5: Verdict

Render one of:
- `PASS` — RHR SLO targets, error budget calculations, and SRP version citations are consistent with the SRP
- `FAIL` — Inconsistencies found (list each with location and severity)
- `WARN` — Minor rounding differences or ambiguities that require human review

## Output Format

```
## SRP → RHR Consistency Check

**Artifacts reviewed:**
- SRP: {SRP-ID} version(s): {v{N}} — Status: Frozen
- RHR: {RHR-ID} — Coverage period: {start} through {end}

**Verdict: PASS / FAIL / WARN**

### SLO Target Consistency

| SLO Name | SRP §2 Target | RHR §2 Target | Match? | SRP §2 Window | RHR §2 Window | Match? |
|----------|-------------|-------------|--------|-------------|-------------|--------|
| {SLO name} | {X.X%} | {X.X%} | Yes / No | {window} | {window} | Yes / No |

*Missing SLOs (in SRP but not in RHR): {list or "None"}*

### Error Budget Calculation Check

| SLO Name | Calculation Check | Policy Threshold Check | Notes |
|----------|-----------------|----------------------|-------|
| {SLO name} | Pass / Fail ({detail if fail}) | Pass / Fail ({detail if fail}) | {notes} |

### SRP Version Citation Check

| Check | Result | Notes |
|-------|--------|-------|
| SRP ID matches | Pass / Fail | {notes} |
| Version number(s) accurate | Pass / Fail | {notes} |
| Multi-version boundary dates stated (if applicable) | Pass / Fail / N/A | {notes} |

### Findings

| Finding Type | Severity | RHR Location | SRP Location | Description |
|-------------|---------|-------------|-------------|-------------|
| {Target mismatch / Calculation error / Dropped SLO / Version mismatch / Freeze condition error} | {Blocking / Warning} | {RHR §N} | {SRP §N} | {Description} |

*If no findings: "No inconsistencies detected."*

### Summary

{1–3 sentences describing the overall consistency state. If FAIL, state what must be corrected in the RHR before validation. If WARN, state what the reviewer should verify.}
```

## Behavioral Rules

- Do not evaluate whether the SRP's SLO targets are appropriate — evaluate whether the RHR correctly applies them.
- Arithmetic errors are always blocking — a wrong number in the error budget table produces an incorrect compliance record.
- If the RHR covers a period where the SRP was revised, but only one SRP version is provided, note this limitation and ask for the missing version before rendering a PASS verdict.
- Base analysis only on what is explicitly stated in both documents — do not infer targets or budgets from context.
- Flag every discrepancy regardless of size; the reliability team decides which require correction.
