# Incident Record

---

## 1. Document Control

| Field | Value |
|-------|-------|
| IR ID | IR-{SERVICE}-{NNN} |
| SRP ID | SRP-{SERVICE}-{NNN} (version: v{N}) |
| Incident Start | {YYYY-MM-DD HH:MM UTC} |
| Incident End | {YYYY-MM-DD HH:MM UTC} |
| Author | {name} |
| Review Date | {YYYY-MM-DD} |
| Status | Draft / Validated / Freeze Pending / Frozen |
| Governance Model Version | 1.0 |
| Prompt Version | {prompt version} |
| Spec Version | {spec version} |
| Principles Version | {principles file versions} |

---

## 2. Incident Classification

| Field | Value |
|-------|-------|
| Severity | {SEV1 \| SEV2 \| SEV3 \| SEV4} |
| Service | {service name} |
| Detection Method | {automated alert \| user report \| engineer observation \| external notification} |
| Detection Timestamp | {YYYY-MM-DD HH:MM UTC} |
| Incident Commander | {named individual} |

---

## 3. Impact Statement

**Users Affected:** {count, percentage, or "internal only"}

**Affected Functionality:** {specific feature or capability name — not "the service was down"}

**Duration:** {N minutes / N hours} (from {HH:MM} to {HH:MM} UTC on {YYYY-MM-DD})

**Scope:** {geographic region, user segment, or environment if not all users}

---

## 4. Incident Timeline

| Time (UTC) | Event | Responder |
|-----------|-------|-----------|
| {YYYY-MM-DD HH:MM} | {event description} | {name} |
| {YYYY-MM-DD HH:MM} | Incident declared (SEV{N}) | {name} |
| {YYYY-MM-DD HH:MM} | {diagnostic step} | {name} |
| {YYYY-MM-DD HH:MM} | {mitigation action — label as [MITIGATION]} | {name} |
| {YYYY-MM-DD HH:MM} | {resolution action — label as [RESOLUTION]} | {name} |
| {YYYY-MM-DD HH:MM} | Incident declared resolved | {name} |

---

## 5. Root Cause Analysis

**Proximate Cause:**
{The immediate trigger — what happened that caused the incident to begin}

**Contributing Factors:**
- {Factor 1: systemic condition, tooling gap, or process gap that enabled the proximate cause}
- {Factor 2: if applicable}

**Systemic Factors Assessment:**
{Assessment of broader systemic conditions — or "None identified: [brief explanation of why no systemic factors were found]"}

---

## 6. Remediation

**Immediate Fix:**
{What was done to stop the incident and restore service}

**Confirmation of Restoration:**
{How normal operation was confirmed — metric value, alert resolution, or manual verification with result}

**Follow-Up Actions:**

| Action | Owner | Deadline | Tracking Reference |
|--------|-------|----------|-------------------|
| {description} | {named individual} | {YYYY-MM-DD} | {ticket ID or task reference} |
| {description} | {named individual} | {YYYY-MM-DD} | {ticket ID or task reference} |

---

## 7. SLO Impact

| SLO Name | Budget Consumed (minutes) | Budget Consumed (%) | Remaining Budget (%) | Policy Implication |
|----------|--------------------------|--------------------|--------------------|-------------------|
| {SLO name} | {N} minutes | {X.X%} of {window} budget | {X.X%} remaining | {threshold action per SRP §3, or "no threshold crossed"} |

**Unaffected SLOs:**
{List SLOs from the active SRP that were not affected by this incident, or "All SLOs affected — see table above"}

**If no SLOs were affected:**
{Explain why: describe the impact and why it did not reach SLO measurement threshold. Do not leave blank.}
