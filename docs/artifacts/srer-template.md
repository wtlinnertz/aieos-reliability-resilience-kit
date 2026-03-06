# Service Reliability Entry Record

---

## 1. Document Control

| Field | Value |
|-------|-------|
| SRER ID | SRER-{PROJECT}-{NNN} |
| Date | {YYYY-MM-DD} |
| Service Summary | {1–2 sentences identifying the service and the release being handed off} |
| Status | Draft / Validated / Freeze Pending / Frozen |
| Governance Model Version | 1.0 |
| Prompt Version | N/A |

---

## 2. Upstream Reference

**Release Record ID:** {RR-{PROJECT}-{NNN}}

**RR Status:** {Frozen}

### RR §7 Fields (captured from Handoff to Layer 6)

**Production State:**
- System/Service: {name and component}
- Version: {version or commit identifier}
- Deployed Configuration: {key config parameters relevant to reliability}

**Current Exposure State:**
{exposure percentage and any active feature flags affecting this service}

**Monitoring Active:**
{list each monitoring item from RR §7 — metrics, dashboards, alert configurations}

**SLO Baseline:**
{SLO values from RR §7, with source identified}

**Open Incidents at Handoff:**
{list any open incidents at handoff time, or "None"}

**Recommended Watch Items:**
{list recommended watch items from RR §7}

---

## 3. Reliability Owner

| Field | Value |
|-------|-------|
| Name | {named individual — not a team} |
| On-Call Contact | {pager endpoint, channel, or equivalent} |
| Scope | {which service or component this person owns} |

---

## 4. Initial SLO Baseline

{For each SLO from RR §7:}

| SLO Name | SLI Metric | Baseline Value | Measurement Window | Source |
|----------|-----------|---------------|-------------------|--------|
| {name} | {metric name} | {value %} | {window} | {source document ID} |
| {name} | {metric name} | {value %} | {window} | {source document ID} |

**Source document(s):** {ORD ID | SRP ID | first-release documentation reference}

---

## 5. Monitoring Confirmation

**Confirmation Date:** {YYYY-MM-DD}

| Monitoring Item | Alert Threshold | Status | Notes |
|----------------|----------------|--------|-------|
| {metric/alert name} | {numeric threshold} | Active | |
| {metric/alert name} | {numeric threshold} | Active | |
| {metric/alert name} | {numeric threshold} | Active | |

**Gaps (if any):**

| Gap Item | Resolution Owner | Target Date |
|----------|-----------------|------------|
| {description} | {named individual} | {YYYY-MM-DD} |

*If no gaps, write: "No monitoring gaps identified."*

---

## 6. Completeness Checklist

Before freezing this record, confirm:

- [ ] SRER ID, date, and service summary present
- [ ] RR ID referenced and status confirmed as Frozen
- [ ] All RR §7 fields captured in §2 (not merely referenced)
- [ ] Named reliability owner with on-call contact and scope
- [ ] SLO baseline captured with source for each SLO
- [ ] All RR §7 monitoring items confirmed active or gaps documented
- [ ] Confirmation date present

---

## 7. Freeze Declaration

By freezing this record, the reliability owner confirms:
- The upstream RR is in Frozen status and the §7 handoff is complete
- The monitoring items listed in §5 are confirmed active as of the confirmation date
- The SLO baseline in §4 traces to the upstream source document
- This record is complete and authorizes SRP generation to begin

**Frozen by:** {name}
**Freeze date:** {YYYY-MM-DD}
**Status:** Frozen
