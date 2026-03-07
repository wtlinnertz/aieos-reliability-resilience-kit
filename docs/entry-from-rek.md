# Entry from Release Exposure Kit (REK)

**You are here because:** The Release Record (RR) is frozen with §7 Handoff to Layer 6 complete.

**What you must bring:**

| Artifact | Status Required | Where to Find It |
|----------|----------------|-----------------|
| Release Record (RR-{PROJECT}-{NNN}) — §7 must be complete | Frozen | REK `docs/artifacts/` in the consuming project |
| Service name and reliability owner (named individual) | N/A — extracted from RR §7 | RR §7 Handoff to Layer 6 |

**First artifact to generate in this kit:** Service Reliability Entry Record (SRER) — human-authored, no prompt

**Where to start:** `docs/session-setup.md` → "Service Reliability Entry Record (SRER)"

**What changes at this boundary:**

- You shift from a release event to ongoing operational ownership. The timeline is no longer a project — it is indefinite service operation.
- The artifact cadence changes: Incident Records (IRs) are triggered by incidents, Reliability Health Reports (RHRs) by calendar. There is no single "completion" moment.
- The reliability owner is now the primary accountable party. This may be a different person than the release owner or engineering lead.

**Common mistakes at this transition:**

| Mistake | How to Avoid |
|---------|--------------|
| Treating SRER as optional because "we have the RR §7 data" | The SRER is the formal entry gate that establishes the reliability owner and confirms the operational baseline is understood. It is not optional. |
| Copying RR §7 data by pointer reference into SRER | SRER must capture the SLO values, failure modes, and measurement methodology directly — not as "see RR §7." |
| Generating SRP immediately without completing the SRER | SRER is the required input to SRP generation. Validate SRER first, then generate SRP. |

**If you arrived here without a complete upstream artifact:**

Stop. Return to REK, complete §7 in the Release Record, freeze, and then re-enter RRK. RRK cannot establish a reliable operational baseline without the §7 handoff package. A pointer to incomplete §7 content does not satisfy the entry gate.

---

*For the full entry flow, see `docs/playbook.md`.*
