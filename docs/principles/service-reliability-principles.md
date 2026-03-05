# Service Reliability Principles

These principles define how this organization thinks about service reliability: what "working correctly" means, how reliability is measured, and how reliability commitments are made and honored.

These are **organizational policy documents** — input material for SRP generation, not governed artifacts. They answer: "What standards does this organization hold?"

---

## 1. SLO Philosophy

### Reliability is a feature

Service reliability is not a secondary concern — it is a property of the product. SLOs translate reliability commitments into measurable, enforceable standards. Without them, "the service was slow" and "the service was down" are opinions. With them, they are facts with consequences.

### SLOs are customer-facing commitments

SLO targets must reflect what users actually need, not what is technically convenient. An SLO that the organization consistently exceeds by wide margins is wasteful. An SLO that is routinely violated without consequence is meaningless. SLOs must be set to be meaningful.

### Targets must have a basis

SLO targets must be grounded in evidence: historical performance, user research, contractual requirements, or a reasoned baseline from a similar service. Targets that are invented without basis tend to be aspirational rather than operational — they cannot support error budget policy.

### Two SLOs are the minimum

A single SLO captures only one dimension of reliability. Every service must have at least two SLOs, typically covering different aspects of behavior (availability and performance, or availability and correctness). The combination creates a more complete picture of health.

### Measurement windows must be explicit

An SLO without a measurement window is ambiguous. "99.9% availability" is not a complete SLO. "99.9% availability over a 30-day rolling window" is. Measurement windows govern how error budgets are calculated and when they reset.

---

## 2. Error Budget Rules

### The error budget is the reliability contract

The error budget is the gap between 100% and the SLO target — it is the amount of unreliability the organization has agreed is acceptable. It is not a sign of low standards. It is a commitment to what users will actually experience.

### Consumption has consequences

Error budget consumption is not a passive metric. When a service is consuming budget faster than sustainable, action is required. The SRP must define what action is required at what consumption level — "freeze new releases," "escalate to engineering leadership," "invoke incident response" — with specific thresholds, not qualitative guidance.

### Budget state informs release decisions

When the error budget is healthy, the organization can take on more risk. When the budget is exhausted or critically depleted, new releases that could further consume budget must be paused or require explicit exception authorization. This connection between reliability state and release decisions is non-negotiable.

### Do not spend budget you have not earned

Error budget for a measurement window is not available until the window begins. Future budget does not offset current consumption. If the current window's budget is exhausted, it is exhausted — decisions must account for this.

---

## 3. Observability Standards

### SLIs must be measurable

A Service Level Indicator is only valid if it is being measured. The monitoring system must have an active metric corresponding to every SLI defined in the SRP. If a metric does not exist in the monitoring system at SRP freeze time, the SRP may not reference it.

### Metric names must match

The metric name in the SRP must match the metric name in the monitoring system. Descriptions, proxies, or paraphrases are not acceptable. "Error rate" is not a metric name. `notification_service_request_error_rate_5m` is a metric name.

### Data sources must be identified

Each SLI must identify its data source: which monitoring system, which service, which instrumentation point. This allows any engineer to reproduce the measurement independently.

### Alert thresholds must be numeric

Burn rate alerts are not effective if their thresholds are qualitative. "Alert when burn rate is high" is not an alert threshold. "Alert when 1-hour burn rate exceeds 14×" is. All alert thresholds in the SRP must be expressed as numeric values.

### Coverage gaps must be documented

If there are known periods when SLI measurement is unavailable or unreliable (planned maintenance, known instrumentation gaps), these must be documented in the SRP. Undocumented coverage gaps in the RHR are a spec violation.

---

## 4. SLO Revision

### SLOs are versioned

When SLO targets, measurement methodology, or error budget policy change, a new SRP version is issued. The previous version is not edited — it remains frozen and on record. All IRs and RHRs reference the SRP version that was active at the time.

### Revisions require justification

An SRP revision must be traceable to a trigger: user feedback, contractual requirement change, post-incident review finding, or organizational decision. SLO changes without justification are not acceptable — they may represent hiding past misses rather than improving standards.

### Downward revisions require explicit authorization

Lowering an SLO target (making it less strict) is a significant reliability commitment change. It requires explicit authorization by the service owner and must be documented with the business justification. Automatic downward revisions in response to poor performance are not permitted.
