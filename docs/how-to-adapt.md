# How to Adapt This Kit

This kit provides the structure, rules, and prompts for reliability governance. Adapting it to your organization means filling in the content that is specific to your context without modifying the governance structure.

---

## What to Adapt

### Organizational Principles

**Files:** `docs/principles/service-reliability-principles.md`, `docs/principles/incident-management-principles.md`

These files define your organization's reliability philosophy. Adapt them to reflect:

- **SLO philosophy**: Does your organization set SLOs based on contractual commitments, user research, historical data, or a combination? What is the minimum number of SLOs required?
- **Error budget policy**: What happens when a budget is exhausted? Is it a team decision or does it escalate?
- **Incident classification**: Are your severity thresholds based on user count, revenue impact, or something else?
- **Post-incident review standards**: How long after an incident must the review be completed? Who is required to participate?

Replace the default policies with your organization's actual policies. Keep the structure (numbered sections, clear policy statements) but change the content to match your standards.

### Alert Thresholds

The SRP template defaults to 14× fast-burn and 3× slow-burn alerts, which are appropriate for 30-day measurement windows. If your organization uses weekly windows or has different budget-consumption targets for alerting, update the defaults in `docs/prompts/srp-prompt.md`.

### Review Cadence

The playbook leaves review cadence as a team decision. If your organization has a standard (e.g., all tier-1 services must produce monthly RHRs), document it in the principles files and update the playbook accordingly.

## What Not to Adapt

### Specs

The specs define what makes an artifact valid. Do not soften hard gates to make artifacts easier to produce. If a hard gate is failing consistently, that usually means the artifact is incomplete, not that the gate is wrong.

If you genuinely need to add a hard gate (your organization requires something the spec does not check), add it. Do not remove existing hard gates.

### Validator Logic

Validators evaluate against specs. If a validator is producing unexpected results, check whether the spec accurately captures your requirements and adjust the spec if needed, not the validator.

### Governance Model

`docs/governance-model.md` is a synchronized copy of the canonical governance model. Do not edit it. If you believe the governance model should change, update `aieos-governance-foundation/governance-model.md` and sync all kit copies.

## Adding Artifact Types

If your organization needs additional governed artifacts (e.g., a capacity planning record, an on-call health report), follow the four-file system:

1. Write the spec first. Define the hard gates before writing anything else
2. Write the validator. This forces you to verify the spec is evaluable
3. Write the template. Structure only, no content rules
4. Write the prompt. Generation behavior, references spec and template

Register the new artifact type in the playbook, index, and CLAUDE.md.

## Tool Bindings

This kit is tool-agnostic. Metric names in examples use generic names. Templates use `{metric name}` placeholders.

When adopting this kit, create a bindings document:

```
docs/bindings/
  monitoring-system-mapping.md   # Maps SLI metric names to your monitoring system queries
  incident-system-mapping.md     # Maps IR tracking references to your ticketing system
  on-call-system-mapping.md      # Maps alert routing to your on-call platform
```

Bindings are not governed artifacts. They have no spec, validator, or prompt. update them when your tooling changes without touching the governed files.

## Scaling to Multiple Services

This kit governs one service's reliability per SRP. For organizations with many services:

1. Each service gets its own SRER and SRP (with appropriate version IDs).
2. IRs and RHRs are per-service, referencing the service's SRP.
3. If multiple services share an SRP (e.g., microservices with identical SLOs), the SRP scope must explicitly list all covered services.

Consider creating a cross-service reliability dashboard using Layer 7 to aggregate RHR Layer 7 feeds across services.

## First-Time Setup Checklist

- [ ] Read `docs/playbook.md` fully before beginning
- [ ] Obtain a frozen Release Record from Layer 5 (Release & Exposure Kit)
- [ ] Review and adapt `docs/principles/` to match your organizational policies
- [ ] Complete the Service Reliability Intake Form (with SLO Calibration prompt if needed)
- [ ] Complete and freeze the SRER
- [ ] Generate and freeze the SRP
- [ ] Establish monitoring for all SLIs defined in the SRP
- [ ] Confirm burn rate alerts are routing to the correct on-call channel
- [ ] Set a review cadence for RHRs
