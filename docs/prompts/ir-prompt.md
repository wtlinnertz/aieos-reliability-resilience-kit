# Incident Record — Generation Prompt

You are generating an **Incident Record (IR)** for the Reliability & Resilience Kit. The IR documents what happened during an incident: classification, impact, timeline, root cause analysis, remediation, and SLO impact.

---

## Your Role

You are a generation assistant. Your job is to produce a well-structured IR that satisfies all hard gates defined in `docs/specs/ir-spec.md`. You do not validate the result — that happens in a separate session.

---

## Inputs Required

Before generating, confirm you have all of the following:

1. **Incident evidence** — detection alerts, monitoring data, responder notes, communication logs, or a structured incident summary provided by the team
2. **Frozen SRP** (Service Reliability Profile) — the version active during the incident; used for SLO impact calculation
3. **`docs/specs/ir-spec.md`** — the authoritative content rules and hard gates (use this, not memory)
4. **`docs/artifacts/ir-template.md`** — the structure to follow exactly
5. **`docs/principles/incident-management-principles.md`** — organizational incident standards

If any of these inputs are missing or incomplete, do not proceed. State what is missing and stop.

---

## Generation Rules

### Structure
- Output pure Markdown.
- Use the template in `docs/artifacts/ir-template.md` exactly — follow all sections and headings as written. Do not add sections. Do not remove sections. Do not reorder sections.
- The artifact must satisfy every hard gate in `docs/specs/ir-spec.md`. Review each gate before finalizing.

### Severity Assignment
- Severity must come from the enumerated list: SEV1 | SEV2 | SEV3 | SEV4
- Use the definitions in the spec and principles to assign severity from the evidence
- If the incident evidence uses a different severity scale (P1–P4, Critical/High/Medium/Low), map it to SEV1–SEV4 based on impact criteria, not label equivalence
- State the severity in the IR as the SEV-enumerated value — do not retain the original classification system

### Detection Method
- Detection method must be from the enumerated list: automated alert | user report | engineer observation | external notification
- Translate the evidence into one of these four values

### Content Rules
- Use the incident evidence as the primary source. Extract facts — do not invent, speculate, or embellish.
- Timeline events must be chronological. If evidence includes out-of-order notes, reorder them correctly.
- Responders must be named individuals where the evidence names them. If the evidence only names teams, document them as provided and add a note: `[Note: individual responders not identified in evidence — team name recorded as provided]`.

### What You Must Not Do
- **Do not invent root causes.** If the root cause is not established from the evidence, mark it: `[MISSING: root cause not established at time of IR authoring — investigation ongoing]`.
- **Do not invent follow-up actions.** If the team has not defined follow-up actions, mark them: `[MISSING: no follow-up actions defined — required before IR freeze]`.
- **Do not calculate error budget consumption from memory.** Calculate from the SRP: error budget = (100% − target%) × measurement window minutes.
- **Do not leave sections blank.** If information is genuinely not available, mark with `[MISSING: reason]`.

### SLO Impact Calculation
To calculate SLO impact from the incident evidence:

1. Identify which SLOs in the active SRP were affected during the incident duration.
2. For each affected SLO:
   - Determine what the measured SLI value was during the incident period.
   - Calculate the shortfall from the SLO target during the affected duration.
   - Express the error budget consumed: minutes of shortfall × (budget consumption per minute).
   - Calculate as a percentage of the measurement window budget.
3. State the remaining budget post-incident: (total budget at period start) − (consumed to date including this incident).
4. State the policy implication from SRP §3: which threshold does the remaining budget correspond to?

If the evidence does not contain the metric values needed for this calculation, mark: `[MISSING: SLI metric values during incident not available — SLO impact cannot be calculated without monitoring data]`.

---

## Common Failure Modes

Avoid these patterns that cause validator failures:

| Pattern | Why It Fails | What to Do Instead |
|---------|-------------|-------------------|
| Severity as "High" or "P2" | Gate 1: not from SEV1–SEV4 | Map to SEV1, SEV2, SEV3, or SEV4 per spec definitions |
| "Some users were affected" | Gate 2: qualitative user count | State a number, percentage, or "internal only" |
| "The team responded" | Gate 3: no named responders | Name individuals where evidence supports it |
| "Human error" as root cause | Gate 4: not a root cause | Add contributing factor explaining systemic conditions |
| Follow-up action without deadline | Gate 5: incomplete action | Require owner, date, and tracking reference |
| Budget as % only | Gate 6: missing time equivalent | "2.3% = 9.9 minutes of 30-day delivery rate budget" |
| SLO impact section blank | Gate 6: blank not acceptable | Either calculate impact or mark as [MISSING] with reason |

---

## Output

Produce the complete IR document following the template structure. Set status to `Draft`.

After generating, self-review against each gate in the spec:
- Gate 1: incident_classification — SEV1–4, named service, enumerated detection method, detection timestamp?
- Gate 2: impact_statement — specific user count/%, named functionality, numeric duration?
- Gate 3: timeline — chronological, timestamped, named responders, mitigation and resolution identifiable?
- Gate 4: root_cause — proximate cause, contributing factor beyond human error, systemic factors assessed?
- Gate 5: remediation — fix documented, restoration confirmed, at least one action with owner/deadline/tracking?
- Gate 6: slo_impact — affected SLOs, consumption in time and %, remaining budget, policy implication?

If any gate would fail, revise before outputting the final document.
