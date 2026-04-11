# Service Reliability Profile

---

## 1. Document Control

| Field | Value |
|-------|-------|
| SRP ID | SRP-{SERVICE}-{NNN} |
| Service Name | {service name} |
| Owner | {team or role — not an individual person} |
| Version | v{N} |
| Status | Draft / Validated / Freeze Pending / Frozen |
| Scope | {which deployment(s) or environment(s) this SRP governs} |
| SRER Reference | {SRER-{PROJECT}-{NNN}} |
| Governance Model Version | 1.0 |
| Prompt Version | {prompt version} |
| Spec Version | {spec version} |
| Principles Version | {principles file versions} |

---

## 2. SLO Definitions

### SLO 1: {SLO Name}

| Field | Value |
|-------|-------|
| SLI Metric | {metric name} |
| Target | {X.X%} |
| Measurement Window | {e.g., 30-day rolling} |
| Basis for Target | {historical data reference / contractual requirement / first-release baseline / user research} |

### SLO 2: {SLO Name}

| Field | Value |
|-------|-------|
| SLI Metric | {metric name} |
| Target | {X.X%} |
| Measurement Window | {e.g., 30-day rolling} |
| Basis for Target | {historical data reference / contractual requirement / first-release baseline / user research} |

*Add additional SLOs as needed. Minimum 2 required.*

---

## 3. Error Budget

### SLO 1: {SLO Name}

| Field | Value |
|-------|-------|
| Error Budget | {100% − target%} = {X.X%} |
| Time Equivalent | {minutes or hours per measurement window} |

### SLO 2: {SLO Name}

| Field | Value |
|-------|-------|
| Error Budget | {100% − target%} = {X.X%} |
| Time Equivalent | {minutes or hours per measurement window} |

### Consumption Policy

| Threshold | Action Required |
|-----------|----------------|
| {X%} consumed | {specific action} |
| {X%} consumed | {specific action} |

**Freeze/Slowdown Condition:** When {SLO name} error budget reaches {X%} consumed, new releases affecting this service require {explicit exception authorization / reliability review / specific escalation}. This condition applies until the budget resets at the next measurement window.

---

## 4. Burn Rate Alerts

### Fast Burn Alert

| Field | Value |
|-------|-------|
| Trigger | {N}× burn rate over {window} (consuming approximately {X%} of monthly budget) |
| Alert Routing | {channel or pager endpoint} |
| Response | {expected first response action} |

### Slow Burn Alert

| Field | Value |
|-------|-------|
| Trigger | {N}× burn rate over {window} (consuming approximately {X%} of monthly budget) |
| Alert Routing | {channel or pager endpoint} |
| Response | {expected first response action} |

*Note: Multiple SLOs may require separate alert configurations. Add rows as needed.*

---

## 5. Measurement Methodology

### SLO 1: {SLO Name}

| Field | Value |
|-------|-------|
| Exact Metric Name | {exact string as in monitoring system} |
| Data Source | {monitoring system name; service; instrumentation point} |
| Calculation Method | {e.g., ratio of successful requests to total requests over 5-minute windows, aggregated to rolling 30-day} |

### SLO 2: {SLO Name}

| Field | Value |
|-------|-------|
| Exact Metric Name | {exact string as in monitoring system} |
| Data Source | {monitoring system name; service; instrumentation point} |
| Calculation Method | {calculation description} |

**Instrumentation Gaps (if any):**

| SLI | Gap Description | Target Date |
|-----|----------------|------------|
| {SLI name} | {description of what is not yet instrumented} | {YYYY-MM-DD} |

*If no gaps, write: "No instrumentation gaps."*

---

## 6. Exclusions

**Maintenance Window Policy:** {Does the organization exclude planned maintenance windows from SLO measurement? Yes / No / Conditional}

**Planned Downtime Handling:** {Do planned downtime events exclude from error budget consumption? Yes / No}

**Exclusion Declaration Process:** {If exclusions apply: advance notice required (specify), who may declare a maintenance window, how it is recorded}

*If no exclusions apply, write: "No exclusions. All downtime counts toward error budget regardless of cause."*

---

## 7. Scope and Exceptions

> **Advisory section** — Required by template structure; evaluated qualitatively. No hard gate applies. Validators note absence but do not block freeze.

**What this SRP does not cover:**
- {service, component, or environment not governed by this SRP}
- {boundary of scope}

**Active Exceptions:**

| Exception | Expiry Date | Authorization Reference |
|-----------|------------|------------------------|
| {description} | {YYYY-MM-DD} | {authorization document or approver} |

*If no exceptions, write: "No active exceptions."*
