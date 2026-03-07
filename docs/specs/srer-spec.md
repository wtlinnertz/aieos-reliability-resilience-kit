# Service Reliability Entry Record — Specification

The Service Reliability Entry Record (SRER) is the entry gate for the Reliability & Resilience Kit. It must be completed before the Service Reliability Profile can be generated. It confirms the upstream Release Record is frozen, names the reliability owner, confirms that monitoring is active, and captures the SLO baseline from the RR §7 handoff section.

This is a **boundary contract**, not a governed artifact. The record is human-authored. It is validated against this spec before SRP generation begins.

---

## What This Artifact Is Not

- **Not a Service Reliability Profile.** The SRER is the entry gate that confirms prerequisites for SRP generation — it does not define SLO targets, error budgets, or measurement methodology. Those belong in the SRP.
- **Not an authorization to release.** The SRER confirms the Release Record is frozen and the system is in production. The release decision was made upstream in the REK.
- **Not a substitute for the Release Record.** The SRER captures the baseline from RR §7; it does not summarize or replace the RR's content.

---

## Purpose

The SRER serves two roles:

1. **Intake gate** — Confirms that the upstream RR is frozen, the reliability owner has accepted accountability, and the monitoring infrastructure described in RR §7 is confirmed active; prevents reliability governance from beginning without a verified handoff
2. **Baseline record** — Freezes the initial SLO baseline and monitoring state so the SRP has an authoritative starting point and deviations are traceable

---

## Upstream Dependencies

- Frozen Release Record (RR) from the Release & Exposure Kit — must be in Frozen status; RR §7 Handoff to Layer 6 must be present and complete

---

## Required Sections

1. Document Control
2. Upstream Reference
3. Reliability Owner
4. Initial SLO Baseline
5. Monitoring Confirmation
6. Completeness Checklist
7. Freeze Declaration

---

## Content Rules

### Document Control
**Rules**
- SRER ID must be present (format: SRER-{PROJECT}-{NNN})
- Date must be present
- A brief service summary must be present (1–2 sentences identifying the service and the release being handed off)

**Failure Examples**
- Missing SRER ID
- Service summary absent or "TBD"

### Upstream Reference
**Rules**
- The RR being handed off must be identified by ID
- The RR status must be confirmed as Frozen (not Draft or Validated)
- The following RR §7 fields must be present in this section, copied or explicitly referenced: production state (system, version, deployed config), current exposure state, monitoring active, SLO baseline (source identified), open incidents, recommended watch items
- Fields may not be summarized as "see RR §7" — they must be captured in the SRER

**Failure Examples**
- RR ID missing or "unknown"
- RR status listed as "Validated" rather than "Frozen"
- RR §7 fields absent or replaced with a pointer to the RR document

### Reliability Owner
**Rules**
- A named individual (not a team or role title) must be identified as reliability owner
- On-call contact information must be present (pager, channel, or equivalent)
- The reliability owner's scope must be stated (what service or component they own)

**Failure Examples**
- "Platform team" — not a named individual
- On-call contact absent
- Scope absent or "all services"

### Initial SLO Baseline
**Rules**
- SLO baseline values from RR §7 must be captured with their source
- Source must be identified: ORD, first-release documentation, or prior SRP version
- At minimum, the metric name, baseline value, and measurement window must be present for each SLO
- SLO values must not be invented or defaulted (e.g., "99.9% is standard") — they must trace to a source

**Failure Examples**
- SLO values listed without source
- "TBD" or "standard SLOs apply" — no values present
- Baseline values differ from RR §7 without documented explanation

### Monitoring Confirmation
**Rules**
- Every monitoring item listed in RR §7 must be explicitly confirmed active in this section
- Alert thresholds must be recorded for each active alert
- If any monitoring item from RR §7 is not yet active, it must be listed as a gap with a resolution owner and target date
- Confirmation date must be present (the date monitoring was verified, not the date the RR was issued)

**Failure Examples**
- Monitoring list empty or references only RR §7 without confirming current status
- Alert thresholds absent
- Gaps not acknowledged — section states "all monitoring active" when gaps exist

---

## Format Requirements

- SRER ID must reference a specific document ID, not an informal nickname
- Reliability owner must be a person's name, not a team name or role title
- On-call contact must be usable — a specific channel, pager endpoint, or equivalent
- SLO baseline source must be a document ID, not a description ("from the ORD")

---

## Completeness Rules

- All five substantive sections must be present and non-empty
- RR must be in Frozen status
- Named reliability owner with contact information
- All RR §7 monitoring items confirmed or gaps documented

---

## Relationship Rules

- The SRER gates SRP generation — no SRP may be generated until the SRER is frozen and validated
- The SRER does not replace the SRP — the SRP follows after the entry gate passes
- If an existing SRP is already frozen for this service, the SRER still must be completed for the new release engagement to confirm the baseline has not materially changed

---

## Hard Gates

1. **document_control** — SRER ID, date, and service summary present
2. **upstream_reference** — RR ID referenced; RR status confirmed as Frozen; RR §7 fields (production state, monitoring, SLO baseline, watch items) present in SRER, not merely referenced
3. **reliability_owner** — Named individual (not team) as reliability owner with on-call contact information and scope stated
4. **initial_slo_baseline** — SLO baseline values captured with source identified; values trace to RR §7 or upstream document; not invented or defaulted
5. **monitoring_confirmed** — All RR §7 monitoring items confirmed active with alert thresholds recorded; gaps documented with resolution owner and target date; confirmation date present
