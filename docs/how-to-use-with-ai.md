# How to Use This Kit with AI

This guide explains how to set up AI sessions for each step in the Reliability & Resilience Kit workflow. Follow the session setup instructions precisely — incorrect session setup is the most common cause of poor artifact quality.

---

## Core Discipline

**One artifact per session.** Do not generate multiple artifacts in the same session.

**Separate generation and validation.** Always validate in a new session. Never ask the AI that generated an artifact to validate it — this produces self-validation bias.

**Include full frozen documents.** Do not summarize upstream artifacts. Provide the complete document.

---

## SRER — Human-Authored (No AI Generation Session)

The SRER is human-authored. Do not use AI to complete it. Complete the template yourself using information from the frozen Release Record.

**Validation session setup:**
```
Documents to provide:
1. The completed SRER (full document)
2. docs/specs/srer-spec.md

Prompt:
"Validate this Service Reliability Entry Record against the SRER spec.
Use only the spec as the source of truth for pass/fail criteria.
Do not suggest improvements. Judge only what is explicitly present.
Output JSON using the format defined in the validator."
```

---

## SRP — Generation Session

**Session setup:**
```
Documents to provide:
1. Completed Service Reliability Intake Form
2. Frozen SRER (full document)
3. docs/specs/srp-spec.md
4. docs/artifacts/srp-template.md
5. docs/principles/service-reliability-principles.md

Prompt:
"Generate a Service Reliability Profile using the provided inputs.
Follow the prompt in docs/prompts/srp-prompt.md.
Use the template exactly. Satisfy all hard gates in the spec.
Do not invent SLO targets or metric names — mark any missing information
with [MISSING: reason]. Output pure Markdown."
```

**After generation:** Review the SRP. Confirm:
- SLO targets are consistent with the SRER baseline (not significantly higher without justification)
- Metric names are exact strings you can verify in your monitoring system
- Error budget calculations are correct: 100% − target% = budget percentage; multiply by window minutes for time equivalent

**Validation session setup:**
```
Documents to provide:
1. The generated SRP (full document)
2. docs/specs/srp-spec.md

Prompt:
"Validate this Service Reliability Profile against the SRP spec.
Use only the spec as the source of truth for pass/fail criteria.
Do not suggest improvements. Judge only what is explicitly present.
Output JSON using the format defined in docs/validators/srp-validator.md."
```

---

## IR — Generation Session

**Session setup:**
```
Documents to provide:
1. Incident evidence (alert records, metric data, responder notes, timeline)
2. Frozen SRP (the version active during the incident — check the version field)
3. docs/specs/ir-spec.md
4. docs/artifacts/ir-template.md
5. docs/principles/incident-management-principles.md

Prompt:
"Generate an Incident Record for this incident using the provided evidence.
Follow the prompt in docs/prompts/ir-prompt.md.
Use the template exactly. Severity must be from SEV1–SEV4.
Calculate SLO impact from the frozen SRP error budget.
Do not invent root causes or follow-up actions — mark missing information
with [MISSING: reason]. Output pure Markdown."
```

**After generation:** Review the IR. Confirm:
- Severity is correctly assigned based on impact criteria (not just internal classification)
- The timeline is chronologically accurate and matches your notes
- Root cause analysis reflects what was learned, not initial speculation
- Follow-up actions have real owners, real deadlines, and real tracking references

**Validation session setup:**
```
Documents to provide:
1. The generated IR (full document)
2. docs/specs/ir-spec.md

Prompt:
"Validate this Incident Record against the IR spec.
Use only the spec as the source of truth for pass/fail criteria.
Do not suggest improvements. Judge only what is explicitly present.
Output JSON using the format defined in docs/validators/ir-validator.md."
```

---

## RHR — Generation Session

**Session setup:**
```
Documents to provide:
1. Frozen SRP (the active version for this coverage period — check version)
2. All frozen IRs from the coverage period (provide each IR in full)
3. Monitoring data export for the coverage period
   (SLO compliance percentages, error budget consumption, burn rate observations)
4. Coverage period start and end dates
5. docs/specs/rhr-spec.md
6. docs/artifacts/rhr-template.md

Prompt:
"Generate a Reliability Health Report for this coverage period using the provided inputs.
Follow the prompt in docs/prompts/rhr-prompt.md.
Cross-reference the SRP to confirm all SLOs are reported.
Calculate error budget state from monitoring data, not from IR durations.
Do not leave pattern observation or Layer 7 fields blank.
Output pure Markdown."
```

**After generation:** Review the RHR. Confirm:
- Every SLO from the SRP has a corresponding compliance entry
- Compliance percentages match what you see in monitoring, not what you expected
- Error budget remaining is correct given consumption across the measurement window
- All IRs from the period are listed by IR ID

**Validation session setup:**
```
Documents to provide:
1. The generated RHR (full document)
2. docs/specs/rhr-spec.md
3. The active frozen SRP (for SLO cross-reference)

Prompt:
"Validate this Reliability Health Report against the RHR spec.
Cross-reference the provided SRP to confirm all SLOs are reported.
Use only the spec as the source of truth for pass/fail criteria.
Do not suggest improvements. Judge only what is explicitly present.
Output JSON using the format defined in docs/validators/rhr-validator.md."
```

---

## Utility Prompts

### Incident Triage (during active incidents)

```
Documents to provide:
1. Current incident state (everything known so far)
2. Active SRP (if available)
3. Monitoring data available at this time
4. Timeline of events so far
5. Recent changes (deployments, config changes, dependency changes in prior 72 hours)

Prompt:
"Perform incident triage analysis using docs/prompts/incident-triage-prompt.md.
Analyze severity, scope, SLO impact projection, response gaps, and risk indicators.
Flag uncertainty explicitly. Output in the format specified in the prompt."
```

### SLO Calibration (before intake form completion)

```
Documents to provide:
1. Historical SLI metric data (at least 30 days, 90+ days preferred)
2. SRER SLO baseline (if available)
3. Service characteristics and criticality information
4. Contractual or regulatory constraints (if any)

Prompt:
"Perform SLO calibration analysis using docs/prompts/slo-calibration-prompt.md.
Analyze the performance distribution and recommend candidate targets.
Output candidate targets with basis in the format specified in the prompt.
All recommendations require human review before use in the intake form."
```

---

## Troubleshooting

**Validator returns FAIL on multiple gates**
Check that the generation session included all required inputs. Missing inputs are the most common cause of multi-gate failures.

**Metric names are flagged as too generic**
The AI may have paraphrased metric names rather than using exact strings. Provide the exact metric names from your monitoring system in the intake form or incident evidence, and regenerate.

**Root cause analysis is shallow**
Provide more complete incident evidence to the generation session — especially responder notes and post-incident review discussion. The IR prompt requires this to produce substantive root cause analysis.

**SLO compliance is difficult to calculate**
Ensure monitoring data export includes SLI values at sufficient granularity for the measurement window. Aggregated data that does not support the compliance calculation must be documented as a measurement gap.

**Error budget time equivalents are incorrect**
Verify the measurement window minutes: 30-day = 43,200 min, 28-day = 40,320 min, 7-day = 10,080 min. The AI uses these constants — if your window is different, state it explicitly in the intake form.
