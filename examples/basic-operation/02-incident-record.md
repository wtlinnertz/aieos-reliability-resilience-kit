# Incident Record

---

## 1. Document Control

| Field | Value |
|-------|-------|
| IR ID | IR-NOTIF-001 |
| SRP ID | SRP-NOTIF-001 (version: v1) |
| Incident Start | 2026-01-29 14:22 UTC |
| Incident End | 2026-01-29 15:07 UTC |
| Author | Marcus Rivera |
| Review Date | 2026-01-31 |
| Status | Frozen |

---

## 2. Incident Classification

| Field | Value |
|-------|-------|
| Severity | SEV3 |
| Service | notification-service |
| Detection Method | automated alert |
| Detection Timestamp | 2026-01-29 14:35 UTC |
| Incident Commander | Marcus Rivera |

---

## 3. Impact Statement

**Users Affected:** Approximately 12% of active users (those who received task assignment notifications during the affected window)

**Affected Functionality:** Email and in-app notification delivery — notifications generated during the window were delayed by 3–12 minutes beyond the 5-minute delivery SLO

**Duration:** 45 minutes (14:22 UTC to 15:07 UTC on 2026-01-29)

**Scope:** All production users; no geographic segmentation; in-app and email delivery paths both affected (shared delivery queue)

---

## 4. Incident Timeline

| Time (UTC) | Event | Responder |
|-----------|-------|-----------|
| 2026-01-29 14:22 | Queue depth begins rising; notification_delivery_queue_depth crosses 450 (no alert threshold at this time) | — |
| 2026-01-29 14:35 | notification_delivery_rate_1h fires at 97.2% (< 98.5% threshold) | System |
| 2026-01-29 14:37 | Marcus Rivera acknowledges alert; begins investigation | Marcus Rivera |
| 2026-01-29 14:40 | Queue depth confirmed at 1,847 items (normal: < 200); delivery workers are processing but queue growing faster than throughput | Marcus Rivera |
| 2026-01-29 14:42 | [MITIGATION] NOTIF_BATCH_SIZE increased from 50 to 150 to accelerate queue drain | Marcus Rivera |
| 2026-01-29 14:45 | Queue depth stabilizes at 1,923; batch size increase insufficient; worker pool suspected as bottleneck | Marcus Rivera |
| 2026-01-29 14:48 | David Park (on-call backup) joins investigation | David Park |
| 2026-01-29 14:52 | Root cause identified: a recurring task assignment event from a bulk import (1,200 tasks) flooded the queue; worker pool (4 workers) insufficient for burst | Marcus Rivera |
| 2026-01-29 14:55 | [MITIGATION] Temporary worker pool scaled to 8 workers | David Park |
| 2026-01-29 15:01 | Queue depth begins draining: 1,100 items | Marcus Rivera |
| 2026-01-29 15:07 | Queue depth reaches 0; delivery rate returns to 99.8%; incident declared resolved | Marcus Rivera |

---

## 5. Root Cause Analysis

**Proximate Cause:**
A bulk task import event generated 1,200 task assignment notifications simultaneously, creating a traffic burst that exceeded the notification delivery worker pool capacity (4 workers). The fixed worker pool was unable to drain the queue fast enough to maintain the 5-minute delivery SLO.

**Contributing Factors:**
- **No queue depth alert**: The delivery queue had no monitoring alert for high depth. The worker pool saturation was not detectable from existing alerts until the delivery rate SLO was already breached. The notification_delivery_rate_1h alert fired 13 minutes after the queue began filling.
- **Fixed worker pool without burst scaling**: The worker pool was configured as a fixed-size deployment (4 replicas). No autoscaling policy existed to increase worker count under sustained queue pressure.

**Systemic Factors Assessment:**
The combination of no queue depth visibility and no burst scaling creates a structural gap: high-volume events (bulk imports, system migrations, marketing campaigns) can predictably saturate the delivery pipeline with no early warning. This gap was identified as a watch item in RR-TASKFLOW-001 §7 ("queue depth under high load") but no action was taken before this incident occurred. The recommended watch item was noted but not translated into a monitoring alert.

---

## 6. Remediation

**Immediate Fix:**
Temporary worker pool scaling from 4 to 8 replicas at 14:55 UTC. Queue drained to zero by 15:07 UTC. Worker pool returned to 4 replicas at 15:30 UTC after confirming no residual queue depth.

**Confirmation of Restoration:**
notification_delivery_rate_1h returned to 99.8% at 15:07 UTC (confirmed in monitoring dashboard, archived at incidents/IR-NOTIF-001/restoration-metrics.png). Queue depth confirmed at 0 items at 15:07 UTC.

**Follow-Up Actions:**

| Action | Owner | Deadline | Tracking Reference |
|--------|-------|----------|-------------------|
| Add queue depth alert: fire at > 500 items with 5-minute sustained threshold, route to #platform-oncall | Marcus Rivera | 2026-02-05 | TASK-4821 |
| Implement autoscaling for delivery worker pool (min: 4, max: 12, scale trigger: queue depth > 300 sustained 3 minutes) | David Park | 2026-02-14 | TASK-4822 |
| Add bulk import notification rate limiting: max 100 notifications/minute per import job to prevent queue floods | Sarah Chen | 2026-02-28 | TASK-4823 |

---

## 7. SLO Impact

| SLO Name | Budget Consumed (minutes) | Budget Consumed (%) | Remaining Budget (%) | Policy Implication |
|----------|--------------------------|--------------------|--------------------|-------------------|
| Delivery Rate | 10.3 minutes | 2.4% of 30-day budget (432 min) | 97.6% remaining | 2.4% consumed; below 50% threshold; no action required per SRP §3 |

**Unaffected SLOs:**
- Error Rate (SLO 1): Not affected. All notification requests returned HTTP 2xx; errors were in delayed delivery, not request failures.
- p99 Latency (SLO 2): Not affected. Request latency remained below 400ms throughout; the delay was in the delivery pipeline, not the request handler.

**Summary:** This incident consumed 10.3 minutes (2.4%) of the 30-day delivery rate error budget. 97.6% of the budget (421.7 minutes) remains for the remainder of the measurement window (which resets on the 30-day rolling basis). No SRP §3 consumption policy thresholds were crossed.
