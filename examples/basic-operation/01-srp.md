# Service Reliability Profile

---

## 1. Document Control

| Field | Value |
|-------|-------|
| SRP ID | SRP-NOTIF-001 |
| Service Name | notification-service |
| Owner | Platform Team |
| Version | v1 |
| Status | Frozen |
| Scope | Production deployment of notification-service (in-app and email notification delivery paths) |
| SRER Reference | SRER-TASKFLOW-001 |

---

## 2. SLO Definitions

### SLO 1: Error Rate

| Field | Value |
|-------|-------|
| SLI Metric | notification_request_error_rate |
| Target | 99.9% |
| Measurement Window | 30-day rolling |
| Basis for Target | First-release baseline from ORD-TASKFLOW-001 §5; consistent with pre-release load testing showing 99.97% under sustained load |

### SLO 2: p99 Latency

| Field | Value |
|-------|-------|
| SLI Metric | notification_p99_latency_ms |
| Target | 99.5% of requests under 400ms |
| Measurement Window | 30-day rolling |
| Basis for Target | First-release baseline from ORD-TASKFLOW-001 §5; user research finding: users notice delays > 400ms in notification display (reference: UX-RESEARCH-2025-11) |

### SLO 3: Delivery Rate

| Field | Value |
|-------|-------|
| SLI Metric | notification_delivery_rate |
| Target | 99.0% of notifications delivered within 5 minutes |
| Measurement Window | 30-day rolling |
| Basis for Target | First-release baseline from ORD-TASKFLOW-001 §5; notification delivery SLA from product requirements (reference: DPRD-TASKFLOW-001 §3) |

---

## 3. Error Budget

### SLO 1: Error Rate

| Field | Value |
|-------|-------|
| Error Budget | 100% − 99.9% = 0.1% |
| Time Equivalent | 0.1% × 43,200 minutes = 43.2 minutes per 30-day window |

### SLO 2: p99 Latency

| Field | Value |
|-------|-------|
| Error Budget | 100% − 99.5% = 0.5% |
| Time Equivalent | 0.5% × 43,200 minutes = 216 minutes per 30-day window |

### SLO 3: Delivery Rate

| Field | Value |
|-------|-------|
| Error Budget | 100% − 99.0% = 1.0% |
| Time Equivalent | 1.0% × 43,200 minutes = 432 minutes per 30-day window |

### Consumption Policy

| Threshold | Action Required |
|-----------|----------------|
| Any SLO reaches 50% consumed | Platform Team reviews planned releases affecting notification-service; risk assessment required before proceeding |
| Any SLO reaches 75% consumed | Engineering Manager notified; new feature releases require explicit sign-off from Engineering Manager |
| Any SLO reaches 100% consumed | New feature releases frozen until measurement window resets; reliability-focused releases only with explicit authorization |

**Freeze/Slowdown Condition:** When any SLO's error budget reaches 100% consumed, new feature releases affecting notification-service require explicit exception authorization from the Engineering Manager. This condition remains active until the current measurement window resets. Reliability-focused releases (incident remediation, observability improvements) may proceed with Platform Team approval.

---

## 4. Burn Rate Alerts

### Fast Burn Alert

| Field | Value |
|-------|-------|
| Trigger | 14× burn rate over 1-hour window (consuming approximately 2% of monthly budget in 1 hour) |
| Alert Routing | #platform-oncall (Slack) + pager: platform-on-call@oncall.example.com |
| Response | On-call engineer investigates within 5 minutes; assess whether active incident should be declared |

### Slow Burn Alert

| Field | Value |
|-------|-------|
| Trigger | 3× burn rate over 6-hour window (consuming approximately 5% of monthly budget in 6 hours) |
| Alert Routing | #platform-oncall (Slack) |
| Response | On-call engineer reviews within 30 minutes; assess whether release freeze or engineering attention required |

*Note: Burn rate calculations apply per SLO. A fast burn on the delivery rate SLO (which has a larger budget) represents a larger absolute number of failed deliveries than a fast burn on the error rate SLO. Both alert independently.*

---

## 5. Measurement Methodology

### SLO 1: Error Rate

| Field | Value |
|-------|-------|
| Exact Metric Name | `notification_service_request_errors_total` / `notification_service_requests_total` |
| Data Source | Prometheus (production cluster); notification-service request handler instrumentation |
| Calculation Method | (total HTTP 5xx responses + total HTTP timeout responses) / total HTTP requests over 5-minute intervals; aggregated to 30-day rolling window using rate() function |

### SLO 2: p99 Latency

| Field | Value |
|-------|-------|
| Exact Metric Name | `notification_service_request_duration_seconds` (histogram) |
| Data Source | Prometheus (production cluster); notification-service request duration histogram |
| Calculation Method | histogram_quantile(0.99, notification_service_request_duration_seconds) evaluated over 5-minute windows; compliance = fraction of 5-minute windows where p99 < 0.4s, aggregated to 30-day rolling |

### SLO 3: Delivery Rate

| Field | Value |
|-------|-------|
| Exact Metric Name | `notification_delivery_completed_total` / `notification_delivery_attempted_total` |
| Data Source | Prometheus (production cluster); notification-service delivery pipeline instrumentation |
| Calculation Method | Count of deliveries confirmed delivered within 300 seconds / total deliveries attempted; calculated over 1-hour windows, aggregated to 30-day rolling window |

**Instrumentation Gaps:** No instrumentation gaps.

---

## 6. Exclusions

**Maintenance Window Policy:** Planned maintenance windows do not exclude from SLO measurement. All downtime counts toward error budget regardless of cause.

**Planned Downtime Handling:** Planned downtime is not excluded from error budget consumption. Engineering teams must account for planned maintenance when assessing budget state before scheduling downtime.

**Exclusion Declaration Process:** Not applicable — no exclusions.

---

## 7. Scope and Exceptions

**What this SRP does not cover:**
- Staging and development environments (production only)
- Notification content generation (governed separately by the content-service SRP)
- External email provider reliability (third-party SLA; monitored but not included in delivery rate SLO calculation)

**Active Exceptions:**

No active exceptions.
