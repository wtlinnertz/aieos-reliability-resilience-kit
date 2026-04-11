# RR → SRER Consistency Check — Utility Prompt

## Purpose

Verify that the Service Reliability Entry Record (SRER) accurately reflects the content of the frozen Release Record §7 (Handoff to Layer 6) it is based on. This check runs at the RRK entry boundary to confirm the SRER's claims about the release and service state are accurate before SRP generation begins.

This is a **utility prompt** — it produces analysis for human review. Its output does not substitute for the SRER Validator, which evaluates the SRER against its spec.

## When to Use

Run this prompt after the SRER has been drafted and before running the SRER Validator — to confirm the SRER's statements about the Release Record §7 handoff content are accurate.

Especially useful when:
- The release team and reliability team are different groups
- Time has passed between RR freeze and the reliability engagement start
- The service was previously monitored under a different SRP and the RR represents a new release

## Inputs Required

1. The frozen Release Record (RR) — specifically §7 (Handoff to Layer 6)
2. The drafted Service Reliability Entry Record (SRER)

## Instructions

### Step 1: SLO Baseline Verification

The RR §7 provides an SLO baseline established during or after the release. The SRER should reflect this baseline. Check:

1. Each SLO metric cited in the SRER's upstream reference section traces to the RR §7 content.
2. SLO targets and measurement windows stated in the SRER match what the RR §7 states.
3. If the RR §7 states no SLO baseline is established (first release), confirm the SRER reflects this correctly — it should not fabricate a baseline.

### Step 2: Monitoring System Verification

The RR §7 should document what monitoring systems were active at the time of release handoff. Check:

1. The monitoring systems cited in the SRER match those documented in the RR §7.
2. The SRER does not claim monitoring is in place for systems the RR did not confirm as operational.
3. Any monitoring gaps or limitations noted in the RR §7 are reflected in the SRER.

### Step 3: Open Incidents and Watch Items Verification

The RR §7 may document open incidents or watch items at the time of release handoff. Check:

1. Each open incident cited in the SRER traces to an item in the RR §7.
2. Watch items or elevated risk areas noted in the RR §7 are reflected in the SRER.
3. The SRER does not silently drop open incidents or watch items that the RR §7 identified.

### Step 4: Service Identification Consistency

Confirm:
1. The service name and scope in the SRER match the service described in the RR §7.
2. The release being handed off (release name/ID if present) is consistently identified in both documents.

### Step 5: Verdict

Render one of:
- `PASS` — SRER accurately reflects RR §7 handoff content; no items silently dropped
- `FAIL` — Discrepancies found (list each with location and severity)
- `WARN` — Minor gaps that do not block progression but require human review

## Output Format

```
## RR → SRER Consistency Check

**Artifacts reviewed:**
- RR: {RR-ID} — Status: {status}
- SRER: {SRER-ID}

**Verdict: PASS / FAIL / WARN**

### Findings

| Finding Type | Severity | RR §7 Location | SRER Location | Description |
|-------------|---------|---------------|--------------|-------------|
| {SLO mismatch / Monitoring gap / Dropped incident / Dropped watch item / Service identity mismatch} | {Blocking / Warning} | {RR §7 subsection} | {SRER §N or "absent"} | {Description} |

*If no findings: "No discrepancies detected."*

### SLO Baseline Coverage

| SLO from RR §7 | Reflected in SRER? | Consistent Target/Window? | Notes |
|---------------|-----------------|--------------------------|-------|
| {SLO name} | Yes / No | Yes / No / N/A | {notes} |

### Open Items Coverage

| RR §7 Item | Type | Reflected in SRER? | Notes |
|-----------|------|------------------|-------|
| {item description} | {Open incident / Watch item} | Yes / No | {notes} |

*If RR §7 contains no open items, state: "RR §7 contains no open incidents or watch items."*

### Summary

{1–3 sentences describing the overall consistency state. If FAIL, state what must be resolved before the SRER can be frozen. If WARN, state what the reliability team should review.}
```

## Behavioral Rules

- Do not evaluate whether the RR §7 content is correct — evaluate whether the SRER accurately reflects it.
- Do not suggest changes to the RR — it is frozen and must not be modified to satisfy the SRER.
- If RR §7 is absent or contains minimal handoff content, flag this explicitly — the SRER cannot accurately reflect what was not documented.
- Base analysis only on what is explicitly stated in both documents — do not infer SLO baselines or monitoring state from context.
- Flag every discrepancy; the reliability team decides which to address and which to accept.
