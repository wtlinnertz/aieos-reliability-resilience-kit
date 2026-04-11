# Service Reliability Profile — Specification

Version: v1.0

The Service Reliability Profile (SRP) is the foundational reliability artifact for a service. It defines what "working correctly" means: the SLO targets the service must meet, the error budget available for each SLO, the burn rate alert thresholds that indicate unsustainable consumption, and the measurement methodology that makes each SLI auditable.

The SRP is **versioned and reused** across incidents and health reports until SLOs change. When SLOs change, a new SRP version is issued; the previous version remains frozen.

---

## What This Artifact Is Not

- **Not a monitoring dashboard configuration.** The SRP defines the standards against which monitoring is configured and evaluated — not the configuration itself. Dashboard setup and alert wiring are operational activities that implement the SRP's specifications.
- **Not an incident response plan.** The SRP defines what "working correctly" means and what burn rate signals indicate budget consumption. Incident response procedures belong in the ORD or runbooks.
- **Not an aspirational target document.** All SLO targets in the SRP must be derived from the SRER baseline and intake form — not from "industry standards" or what would be ideal. Targets must reflect what the service actually achieves as a minimum, not what it aspires to.

---

## Purpose

The SRP serves three roles:

1. **Reliability contract** — Defines the measurable standards the service is expected to meet, enabling objective pass/fail assessment
2. **Error budget policy** — Translates SLO targets into actionable budget thresholds with defined consequences for consumption
3. **Measurement standard** — Specifies exactly how each SLI is measured, making the reliability picture auditable and reproducible

---

## Upstream Dependencies

- Frozen Service Reliability Entry Record (SRER) — provides initial SLO baseline and monitoring confirmation
- Organizational principles: `service-reliability-principles.md` — defines SLO philosophy and observability standards
- Service Reliability Intake Form (completed by human) — structured human input for generation

---

## Required Sections

1. Document Control
2. SLO Definitions
3. Error Budget
4. Burn Rate Alerts
5. Measurement Methodology
6. Exclusions
7. Scope and Exceptions

---

## Content Rules

### Document Control
**Rules**
- SRP ID must be present (format: SRP-{SERVICE}-{NNN})
- Service name must be present
- Owner must be present as a team or role, not an individual (the SRP is a service-level document, not a person-level one)
- Version must be present (format: v{N}, e.g., v1, v2)
- Status must be one of: Draft | Validated | Frozen
- Scope must state which deployment(s) or environment(s) this SRP governs (e.g., "production deployment of notification-service")

**Failure Examples**
- SRP ID absent
- Owner listed as an individual person's name
- Version absent or listed as "initial"
- Scope absent or "all environments"

### SLO Definitions
**Rules**
- At least 2 SLOs must be defined
- Each SLO must have:
  - A name (human-readable label for this SLO)
  - A named SLI metric (the specific metric being measured)
  - A target percentage (e.g., 99.9%)
  - A measurement window (e.g., 30-day rolling, 28-day rolling)
  - A basis for the target (evidence source: historical data, contractual requirement, first-release baseline, user research)
- SLO names must be unique within this SRP

**Failure Examples**
- Only one SLO defined
- SLO target stated without measurement window ("99.9% availability")
- Basis for target absent or stated as "industry standard" without reference
- SLI metric absent or described qualitatively rather than named

### Error Budget
**Rules**
- Error budget must be calculated and stated per SLO (formula: 100% − target%)
- Error budget must be expressed in both percentage and time equivalent for the measurement window
- Consumption policy must be stated: what happens at specific consumption thresholds (e.g., "at 50% consumption: review and assess new releases; at 100% consumption: freeze new feature releases pending reliability review")
- Freeze/slowdown condition must be stated: the specific consumption level at which new releases affecting this service require explicit exception authorization

**Failure Examples**
- Budget stated as percentage only, without time equivalent
- Consumption policy absent
- Freeze condition absent or stated qualitatively ("when budget is low")

### Burn Rate Alerts
**Rules**
- A fast burn alert must be defined: typically 14× or greater burn rate over a 1-hour window, consuming approximately 2% of the monthly budget
- A slow burn alert must be defined: typically 3× or greater burn rate over a 6-hour window, consuming approximately 5% of the monthly budget
- Both alerts must be expressed as numeric thresholds (not described qualitatively)
- Alert routing must be defined: where does each alert go (on-call channel, pager, escalation path)
- Fast and slow burn definitions may be adjusted for the measurement window (e.g., weekly windows require different multipliers) but must include the calculation rationale

**Failure Examples**
- Only one burn rate alert defined
- Alert thresholds described qualitatively ("high burn rate")
- Alert routing absent or "default alerts"
- Thresholds stated without the budget consumption basis

### Measurement Methodology
**Rules**
- For each SLI defined in §2, the measurement methodology section must include:
  - The exact metric name as it appears in the monitoring system
  - The data source (monitoring system, service, instrumentation point)
  - The calculation method (e.g., "ratio of successful requests to total requests over 5-minute windows, aggregated to rolling 30-day")
- Metric names must be exact — "error rate" is not acceptable; `notification_service_request_errors_total` is
- If any SLI metric is not yet instrumented, it must be marked as a gap with a target instrumentation date; the SRP may not claim it is measuring something it is not

**Failure Examples**
- Metric names described rather than named exactly
- Data source absent
- Calculation method absent or "standard monitoring"
- Gap present but not documented

### Exclusions
**Rules**
- Maintenance window policy must be stated: do planned maintenance windows exclude from SLO measurement or not?
- Planned downtime handling must be stated explicitly: does this organization exclude planned downtime from error budget consumption?
- If exclusions apply, the process for declaring a window (advance notice required, who can declare) must be stated

**Failure Examples**
- Exclusions section absent
- "TBD" — policy must be stated even if the answer is "no exclusions"
- Exclusions described vaguely ("as needed") rather than with a defined process

### Scope and Exceptions
**Rules**
- This section must state what is not covered by this SRP
- Any known gaps in measurement coverage must be documented
- Any time-bounded exceptions (e.g., "delivery rate SLO suspended for 30 days pending instrumentation fix") must include an expiry date and authorization reference

**Failure Examples**
- Section absent
- Exceptions stated without expiry date or authorization reference

---

## Format Requirements

- SLO targets must be expressed as percentages with at least one decimal place (e.g., 99.9%, not "high availability")
- Error budget time equivalents must include units (minutes/month, hours/month, or equivalent)
- All metric names must be exact strings matching the monitoring system
- Alert thresholds must be expressed as numeric multipliers (e.g., "14×") with window durations

---

## Completeness Rules

- All seven sections must be present and non-empty
- At least 2 SLOs defined
- Error budget calculated for each SLO
- Both fast burn and slow burn alerts defined
- Measurement methodology covers every SLI defined in §2
- Exclusions policy stated (even if "no exclusions")

---

## Relationship Rules

- The SRP is versioned — a new version is required when SLO targets, methodology, or error budget policy change
- A frozen SRP may not be edited — changes require a new version via the SRP revision protocol (see playbook)
- IRs and RHRs must reference the SRP version active at the time of the incident or review period
- If a new SRP version is issued mid-review-period, the RHR must note the version change and report compliance separately for each version's coverage period

---

## Advisory Sections

The following template sections are required by template structure but are evaluated qualitatively — no hard gate applies. Validators note absence but do not block freeze.

- **§7 Scope and Exceptions** — Required in template structure. Validators note absence but do not block freeze. Content is evaluated qualitatively: scope should clarify what this SRP does not govern; exceptions should include expiry date and authorization reference.

## Hard Gates

1. **document_control** — SRP ID, service name, owner (team/role not individual), version, status, and scope present
2. **slo_definitions** — At least 2 SLOs defined; each SLO has named SLI metric, target %, measurement window, and basis for target
3. **error_budget** — Budget calculated per SLO (100% − target%) in both percentage and time equivalent; consumption policy stated; freeze/slowdown condition stated
4. **burn_rate_alerts** — Fast burn alert (numeric threshold, window, budget consumption basis) and slow burn alert (numeric threshold, window, budget consumption basis) defined; alert routing defined for both
5. **measurement_methodology** — Each SLI has exact metric name (as in monitoring system), data source, and calculation method; uninstrumented SLIs documented as gaps with target dates
6. **exclusions** — Maintenance window policy stated; planned downtime handling stated; process for exclusion declarations stated if exclusions apply
7. **standard_enforceability** — All standards are specific and numeric; no "should," "ideally," "when possible," or equivalent aspirational language
