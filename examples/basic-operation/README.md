# Example: TaskFlow notification-service Operations

This example shows the complete Layer 6 flow for the TaskFlow notification service after its initial production release.

---

## Scenario

TaskFlow is a fictional SaaS project management tool. The notification-service is responsible for delivering in-app and email notifications to users when tasks are updated or assigned.

The notification-service was released in the prior layer (Release & Exposure Kit) and the Release Record (RR-TASKFLOW-001) was frozen with:
- notification-service v1.2.0 deployed at 100% exposure
- Three monitoring alerts active
- SLO baseline from the ORD: error rate 99.9%, p99 latency 99.5%, delivery rate 99.0%
- No open incidents at handoff
- Recommended watch items: queue depth under high load, email delivery pipeline latency

The Layer 6 engagement covers the first 30 days of production operation.

---

## Artifacts

| File | Artifact | Status |
|------|----------|--------|
| `00-service-reliability-entry.md` | SRER-TASKFLOW-001 | Frozen |
| `01-srp.md` | SRP-NOTIF-001 (v1) | Frozen |
| `02-incident-record.md` | IR-NOTIF-001 | Frozen |
| `03-rhr.md` | RHR-NOTIF-001 | Frozen |

---

## What Happened During the Period

1. **Day 1**: Marcus Rivera (notification platform team) completes the SRER, confirming the RR §7 handoff from the REK team, verifying monitoring is active, and establishing the SLO baseline.

2. **Day 2–3**: The team completes the intake form and generates the SRP. Three SLOs are defined: error rate (99.9%), p99 latency (99.5%), and delivery rate (99.0%). 30-day rolling windows. Fast and slow burn alerts configured.

3. **Day 18**: A SEV3 incident occurs. Queue backpressure causes the delivery rate to drop to 97.2% for 45 minutes. The incident consumes 2.4% of the monthly delivery rate error budget. A queue depth alert is added as a remediation action.

4. **Day 31**: The 30-day review period closes. The RHR synthesizes: error rate SLO met (99.98%), latency SLO met (99.7%), delivery rate SLO met (99.1% — one incident, budget at 97.6% remaining). Layer 7 feed: stable overall, one systemic gap addressed.

---

## How to Read This Example

Follow the artifacts in order (00 → 01 → 02 → 03). Each artifact builds on the previous:

- The SRER establishes the baseline from the RR handoff
- The SRP defines the SLO commitments and error budget policy
- The IR documents what went wrong and what was done
- The RHR summarizes the full period and feeds Layer 7

The `validator-outputs/` directory contains the PASS JSON for each artifact, showing what a successful validation looks like.

---

## Names and Data

All names are fictional. Marcus Rivera is a placeholder reliability owner. TaskFlow is a fictional product. All metric names, thresholds, and values are illustrative.
