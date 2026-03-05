# Service Reliability Intake Form

This form is completed by the service team before SRP generation begins. The AI uses this form, the frozen SRER, and the organizational principles to generate the Service Reliability Profile.

**Instructions:** Fill in all sections. Mark unknown or to-be-determined fields with `[MISSING: reason]` — do not leave fields blank. The AI will not invent information for blank fields.

---

## Section 1: Service Context

**Service name:**

**Service description (2–3 sentences: what does this service do for users?):**

**Owner team or role:**

**Dependencies (services this service calls or relies on):**

**Criticality assessment (how do users or the business notice if this service degrades?):**

---

## Section 2: SLO Intent

For each SLO you intend to define, provide the following. Add additional blocks as needed. Minimum 2 SLOs required.

### SLO A

**What aspect of reliability does this SLO measure?**
(e.g., whether requests succeed, whether they are fast enough, whether notifications are delivered)

**SLI — what is the metric?**
(name the specific behavior being measured)

**Target percentage you want to commit to:**

**Measurement window:**
(e.g., 30-day rolling, 28-day rolling, monthly calendar)

**Basis for this target:**
(options: historical performance data [provide reference], contractual requirement [cite], user research finding [describe], first-release baseline from SRER [confirm], other [explain])

**Is there a contractual or regulatory floor for this SLO?**

---

### SLO B

**What aspect of reliability does this SLO measure?**

**SLI — what is the metric?**

**Target percentage:**

**Measurement window:**

**Basis for this target:**

**Is there a contractual or regulatory floor?**

---

*(Duplicate this block for SLO C, D, etc. as needed)*

---

## Section 3: Error Budget Policy

**At what error budget consumption level should the team review new releases?**
(e.g., 50% consumed → review required)

**At what consumption level should new feature releases be frozen?**
(e.g., 100% consumed → freeze until window resets)

**Who authorizes a freeze exception if a release is urgent?**

---

## Section 4: Burn Rate Alert Preferences

**What is your preferred fast burn alert threshold?**
(recommended: 14× over 1 hour; state if you want a different threshold and why)

**What is your preferred slow burn alert threshold?**
(recommended: 3× over 6 hours; state if you want a different threshold and why)

**Where should burn rate alerts be routed?**
(on-call channel name, pager endpoint, escalation path)

---

## Section 5: Measurement Methodology

For each SLO, provide the exact metric name as it appears in your monitoring system, the data source, and the calculation method.

### SLO A

**Exact metric name in monitoring system:**

**Data source (monitoring system, service name, instrumentation point):**

**Calculation method:**
(e.g., HTTP 2xx responses / total HTTP responses over 5-minute intervals, aggregated to rolling 30-day window)

---

### SLO B

**Exact metric name in monitoring system:**

**Data source:**

**Calculation method:**

---

**Are there any SLIs not yet instrumented in the monitoring system?**
If yes, list them with a target instrumentation date:

---

## Section 6: Exclusions

**Does your organization exclude planned maintenance windows from SLO measurement?**

**Does your organization exclude planned downtime from error budget consumption?**

**If yes to either: describe the process for declaring a maintenance window (advance notice, who can declare, how it is recorded):**

---

## Section 7: Scope Boundaries

**What is NOT covered by this SRP?**
(environments, components, or conditions explicitly out of scope)

**Are there any known temporary exceptions to the SLO targets?**
(e.g., instrumentation not yet complete, known degradation during migration)
If yes, state the exception, expiry date, and authorization reference.

---

## Intake Checklist

Before submitting this form for SRP generation:

- [ ] All SLOs have a named metric, target, window, and basis
- [ ] At least 2 SLOs defined
- [ ] Error budget thresholds and freeze conditions stated
- [ ] Burn rate alert preferences and routing stated
- [ ] Exact metric names confirmed against monitoring system
- [ ] Uninstrumented SLIs identified with target dates
- [ ] Exclusion policy stated
- [ ] Scope boundaries stated
