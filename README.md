# aieos-reliability-resilience-kit

**Layer 6 of the AIEOS system — Reliability & Resilience**

This kit governs how production systems are monitored, measured, and kept running. It receives a frozen Release Record (RR) from the Release & Exposure Kit and produces reliability records that document what "working correctly" means, what went wrong and how it was resolved, and how the service is performing over time — feeding reliability signals downstream to Layer 7.

---

## What This Kit Does

The Release & Exposure Kit (Layer 5) produces a Release Record that declares the release complete, confirms monitoring is active, and documents an SLO baseline. But "monitoring active" is not the same as "reliability governed." This kit governs the gap:

- **SLO definition** — What does "working correctly" mean for this service? (error rates, latency, delivery guarantees)
- **Error budgets** — How much unreliability is acceptable before action is required?
- **Burn rate alerts** — When are SLOs being consumed faster than sustainable?
- **Incident management** — What happened, who responded, what was the root cause?
- **Periodic health reviews** — Is the service trending better or worse over time?

---

## Artifact Types

This kit produces three governed artifact types plus an entry gate:

| Step | Artifact | Purpose |
|------|----------|---------|
| 0 | Service Reliability Entry Record (SRER) | Entry gate: confirms RR is frozen, ownership accepted, monitoring confirmed |
| 1 | Service Reliability Profile (SRP) | SLO definitions, error budgets, burn rate alerts — the "what does working mean?" document |
| 2 | Incident Record (IR) | Per-incident evidence from detection through post-incident review |
| 3 | Reliability Health Report (RHR) | Periodic review of SLO compliance, error budget state, and incident patterns |

Each governed artifact type has exactly four governing files: spec, template, prompt, validator.

---

## Quick Start

1. Read `docs/playbook.md` — the complete process definition
2. Read `docs/how-to-use-with-ai.md` — session setup and AI tool guidance
3. See `examples/basic-operation/` — a worked example (TaskFlow notification-service operation)

---

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
examples/
  basic-operation/     # Worked example: TaskFlow notification-service operation
tests/
  kit-test-plan.md     # Structural integrity checks and flow scenarios
CLAUDE.md              # AI operating instructions
```

---

## AIEOS Layer

| Layer | Kit | Status |
|-------|-----|--------|
| 2. Product Intelligence | `aieos-product-intelligence-kit` | Built |
| 4. Engineering Execution | `aieos-engineering-execution-kit` | Built |
| 5. Release & Exposure | `aieos-release-exposure-kit` | Built |
| **6. Reliability & Resilience** | **`aieos-reliability-resilience-kit`** | **Built** |
| 7. Insight & Evolution | `aieos-insight-evolution-kit` | Planned |

See `aieos-governance-foundation/docs/layer-model.md` for the full seven-layer model.
