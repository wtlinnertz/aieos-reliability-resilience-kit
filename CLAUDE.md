# CLAUDE.md — Reliability & Resilience Kit

## What This Repository Is

This is the **Reliability & Resilience Kit** — an AIEOS kit that governs how production systems are monitored, measured, and kept running. It is Layer 6 of the AIEOS system. It receives a frozen Release Record from the Release & Exposure Kit and governs SLO definition, error budget management, incident documentation, and periodic health reporting.

## Repository Structure

```
docs/
  principles/          # Organizational reliability policy (input material)
  specs/               # Content rules and hard gates per artifact type
  artifacts/           # Templates and intake forms
  prompts/             # AI generation + utility prompts
  validators/          # Quality gate definitions
  playbook.md          # End-to-end process definition
  index.md             # Documentation entry point
  how-to-adapt.md      # Organizational adoption guidance
  how-to-use-with-ai.md # AI tool usage guide
  governance-model.md  # AIEOS structural rules (reference)
examples/              # Worked example (TaskFlow notification-service operation)
tests/                 # Structural integrity checks
```

## Artifact Types

This kit produces three governed artifact types plus an entry gate:

1. **Service Reliability Profile (SRP)** — SLO definitions, error budgets, burn rate alert thresholds, and measurement methodology. Versioned and reused until SLOs change.
2. **Incident Record (IR)** — Per-incident evidence artifact from detection through post-incident review.
3. **Reliability Health Report (RHR)** — Periodic review capturing SLO compliance, error budget state, incident summary, and Layer 7 feed.

Each artifact type has exactly four governing files: spec, template, prompt, validator.

Plus one entry gate:

- **Service Reliability Entry Record (SRER)** — Step 0 gate (human-authored). Confirms the RR is frozen, names the reliability owner, confirms monitoring is active, and captures the SLO baseline from RR §7. Validated against `srer-spec.md` before SRP generation begins.

## Utility Prompts

Six utility prompts support the flow but do not produce governed artifacts:

- **Incident Triage** (`incident-triage-prompt.md`) — Adversarial analysis of an active incident to prioritize response and identify gaps
- **SLO Calibration** (`slo-calibration-prompt.md`) — Recommends SLO targets from historical baseline data before SRP authoring
- **Escalation Assessment** (`escalation-assessment-prompt.md`) — Evaluates a frozen IR or RHR against the four AIEOS escalation triggers; produces a structured escalation record if a trigger is confirmed
- **Portfolio Health** (`portfolio-health-prompt.md`) — Synthesizes N RHRs across multiple services to surface portfolio-level SLO compliance, error budget risk, and cross-service reliability patterns
- **RR → SRER Consistency Check** (`rr-srer-consistency-check-prompt.md`) — Cross-boundary check verifying the SRER accurately reflects RR §7: SLO baseline, monitoring systems, open incidents, and watch items. Run after SRER draft and before the SRER Validator.
- **SRP → RHR Consistency Check** (`srp-rhr-consistency-check-prompt.md`) — Intra-kit check verifying RHR SLO targets match SRP §2, error budget calculations are consistent with SRP §3, and SRP version citations are accurate. Run after RHR draft and before the RHR Validator.

## Key Rules

- **Specs are the source of truth** — prompts and validators reference specs, never inline rules
- **Validators judge, they do not help** — no suggestions, no redesign
- **Freeze before promote** — RR must be frozen before SRER; SRER must be frozen before SRP generation; frozen SRP is the authoritative input for IR and RHR
- **Separate generation and validation** — different AI sessions to prevent self-validation bias
- **No scope expansion** — downstream artifacts must not expand scope beyond upstream
- **No inferred information** — mark missing information explicitly, do not fill gaps
- **SRP is versioned** — when SLOs change, issue a new SRP version; do not edit a frozen SRP
- **Governance model sync** — `docs/governance-model.md` is a synchronized copy of `aieos-spec/governance-model.md` (canonical authority). Do not edit kit copy directly; update `aieos-spec` first, then sync all kit copies to match exactly. See governance-model.md §15 for versioning and change protocol.
- **Engagement Record** — RRK maintains the Layer 6 section of the project's ER. Add artifact IDs as they freeze, maintain the IR list as incidents occur, and note SRP version changes. See `docs/playbook.md §Maintaining the Engagement Record` and `aieos-spec/docs/engagement-record-spec.md`.

## Artifact Flow

```
Step 0: Service Reliability Entry Record → validate → freeze
Step 1: Service Reliability Profile → generate from intake + SRER + principles
        → validate → freeze (versioned; reused until SLOs change)
[Active Operation — human + monitoring tooling]
Step 2: Incident Record → generate per incident from evidence + frozen SRP
        → validate → freeze
Step 3: Reliability Health Report → generate from IR history + SRP + coverage period
        → validate → freeze → handoff to Layer 7
```

## Boundary Contracts

- **Upstream:** Receives a frozen Release Record from the Release & Exposure Kit (Layer 5). The RR §7 Handoff to Layer 6 section is the upstream boundary contract — it provides the production state, active monitoring, SLO baseline, open incidents, and recommended watch items. Confirmed via SRER.
- **Downstream:** Produces frozen RHRs that Layer 7 (Insight & Evolution) uses as reliability trend input. The RHR §5 Layer 7 Feed section is the downstream boundary contract — it provides trend direction, systemic issues, improvement signals, and recommended continued watch items.

## File Naming

| Type | Pattern |
|------|---------|
| Spec | `{type}-spec.md` |
| Template | `{type}-template.md` |
| Prompt | `{type}-prompt.md` |
| Validator | `{type}-validator.md` |
| Utility Prompt | `{function}-prompt.md` |
| Intake Form | `{context}-intake-template.md` |

## When Working on This Kit

- Read the playbook (`docs/playbook.md`) for the full process definition
- Read the governance model (`docs/governance-model.md`) for structural rules
- Check `docs/how-to-use-with-ai.md` for session setup instructions
- Reference `examples/basic-operation/` for a complete worked example

## Building or Auditing AIEOS Kits

- `aieos-spec/docs/kit-structure-standard.md` — compliance checklist for building and auditing kits
- `aieos-spec/docs/philosophy.md` — design rationale for governance model decisions
- `aieos-spec/docs/layer-model.md` — seven-layer model and kit registry
