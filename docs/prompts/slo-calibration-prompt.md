# SLO Calibration — Utility Prompt

**Type:** Utility prompt (non-governed)
**Purpose:** Recommends SLO targets from historical baseline data before Service Reliability Profile authoring begins.

This prompt does not produce a governed artifact. Its output is a structured recommendation that the service team reviews, adjusts based on their knowledge and constraints, and uses as input to the Service Reliability Intake Form. The human team — not this prompt — makes the SLO commitment.

---

## When to Use This Prompt

Use this prompt when:
- A service is entering Layer 6 for the first time and SLO targets are not established
- An SRP revision is being considered and the team wants a data-driven basis for target changes
- The SRER captures a baseline from RR §7 but the team wants analysis before committing to SRP targets

Do not use this prompt as a substitute for the intake form or as a direct source for SRP values. All outputs from this prompt require human review before use.

---

## Inputs Required

Provide as much of the following as is available:

1. **Historical performance data** — SLI metric values over recent periods (at minimum 30 days; 90+ days preferred)
   - Include: metric name, measurement window, data points or summary statistics (percentile values, average, worst periods)
2. **SRER SLO baseline** (if available) — the baseline captured from RR §7
3. **Service characteristics** — what the service does, who depends on it, what "slow" or "down" means to users
4. **Contractual or regulatory constraints** — any hard floor below which the SLO cannot be set
5. **Organizational risk appetite** — how much unreliability is acceptable; is this a tier-1 service or lower-priority?
6. **Active SRP of a comparable service** (optional) — for comparison if a similar service already has calibrated SLOs

---

## Calibration Framework

### Step 1: Establish the performance distribution

From the historical data, characterize the performance distribution for each SLI:
- 50th percentile (median)
- 90th percentile
- 99th percentile
- Worst observed period (and its cause, if known)

### Step 2: Identify natural reliability levels

Locate the natural "elbow" in the distribution — where performance is typically stable but occasionally degrades. This often corresponds to the 95th–99th percentile range and indicates what the service actually delivers without heroic effort.

### Step 3: Apply the SLO calibration heuristic

SLO targets should be set to be meaningful but achievable:
- **Too strict** (e.g., 99.99% when historical p99 is 99.9%): error budget depletes rapidly with normal operation; creates constant alert fatigue and freeze conditions
- **Too loose** (e.g., 99.0% when historical p50 is 99.95%): SLO is never meaningfully tested; provides no signal when things degrade
- **Recommended range**: Target should require intentional effort to maintain but should not require heroic intervention to avoid missing

A common heuristic: set the target at approximately the 85th–90th percentile of recent performance, excluding known anomalous periods with documented causes.

### Step 4: Analyze candidate targets

For each SLI, evaluate 2–3 candidate targets:

For each candidate:
- **Historical compliance**: What percentage of recent periods would have met this target?
- **Error budget at target**: How many minutes of budget per measurement window?
- **Implied incident tolerance**: How many incidents of what duration can occur before the budget is exhausted?
- **Burn rate sensitivity**: At what incident rate does fast-burn alerting fire?

### Step 5: Flag constraints and risks

- If contractual floor is close to historical performance: flag the risk that normal variation could trigger SLO breaches
- If historical data shows high variance: note that the data may not represent stable operation
- If the SRER baseline differs significantly from historical data: note the discrepancy and ask for clarification

---

## Output Format

Structure your analysis as:

### Performance Summary

**SLI: [metric name]**
- 50th percentile: [value]
- 90th percentile: [value]
- 99th percentile: [value]
- Worst observed: [value] ([period], [cause if known])

### Candidate Targets

**Conservative target: [X.X%]**
- Historical compliance: [% of periods meeting this target]
- Error budget: [X.X%] = [N minutes / measurement window]
- Assessment: [why this target is conservative; when to choose it]

**Recommended target: [X.X%]**
- Historical compliance: [% of periods meeting this target]
- Error budget: [X.X%] = [N minutes / measurement window]
- Assessment: [why this target is recommended; what operational discipline it requires]

**Ambitious target: [X.X%]**
- Historical compliance: [% of periods meeting this target]
- Error budget: [X.X%] = [N minutes / measurement window]
- Assessment: [what changes would be needed to maintain this target; risks]

### Recommendations

1. [Specific recommendation for primary SLO, with basis]
2. [Specific recommendation for secondary SLO or measurement window, with basis]

### Constraints and Risks

- [Any hard floors from contracts or regulations]
- [Any data quality concerns]
- [Any factors that could make historical data unrepresentative of future performance]

---

## Constraints

- All recommendations require human review before use in the intake form
- This output is advisory — the service team makes the SLO commitment, not this prompt
- Do not recommend SLO targets below contractual or regulatory floors even if historical data supports it
- Flag uncertainty in historical data rather than presenting uncertain data as confident conclusions
- Do not recommend targets based on what other organizations claim to achieve — only on the data provided
