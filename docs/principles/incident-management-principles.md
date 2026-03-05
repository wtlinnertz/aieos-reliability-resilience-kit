# Incident Management Principles

These principles define how this organization classifies, responds to, and learns from incidents. They govern when incidents are declared, how severity is assigned, and what evidence standards apply to incident documentation.

These are **organizational policy documents** — input material for IR generation, not governed artifacts. They answer: "What standards does this organization hold?"

---

## 1. Incident Classification

### Severity is an impact statement, not a feeling

Severity levels exist to allocate response resources and set expectations — not to communicate urgency or stress. SEV1 means a complete outage affecting all users. SEV4 means a minor issue with minimal impact and a workaround available. Assigning SEV1 to a SEV3 incident because responders are alarmed is harmful: it burns response capacity and desensitizes the organization to real SEV1 events.

### Severity must be declared at detection, updated if impact changes

Severity is assessed as soon as an incident is declared and may be upgraded or downgraded as the scope of impact becomes clearer. A declared severity must not remain unchanged simply because the incident is ongoing — if it becomes clear that a SEV2 is actually a SEV1, it must be upgraded immediately.

### Enumerated severity levels

| Severity | Definition |
|----------|-----------|
| SEV1 | Complete service outage — no users can access the service or core functionality |
| SEV2 | Major degradation — significant percentage of users are affected or core functionality is severely impaired |
| SEV3 | Partial degradation — a subset of users or non-core functionality is affected; service remains available |
| SEV4 | Minor issue — minimal user impact; workaround available; not time-critical |

### When to declare an incident

An incident should be declared whenever:
- An SLO breach is occurring or imminent
- A burn rate alert has fired
- A customer-visible service failure is occurring, even if the monitoring system has not yet alerted
- Any engineer believes a situation warrants coordinated response

The cost of declaring an unnecessary incident is low. The cost of failing to declare a necessary incident can be severe.

---

## 2. Response Escalation

### Named responders, not teams

Every incident must have a named individual as the incident commander and named responders. "The on-call team responded" is not acceptable — it prevents accountability and makes post-incident analysis impossible.

### Detection method must be recorded

How an incident was detected matters for systemic improvement. Was it detected by a user report, an automated alert, or an engineer noticing anomalous behavior? Detection method informs whether alerting coverage is adequate.

### Timestamps are required at every step

Incident timelines must include timestamps for: first detection, declaration, each mitigation step, resolution, and declaration of end of incident. Clock time is required — "around noon" is not a timestamp.

### Mitigation and resolution are distinct

Mitigation reduces the impact of an incident but does not necessarily address the root cause. Resolution addresses the root cause and restores normal operation. These are different events and must be recorded separately in the timeline. "We restarted the service" may be mitigation. "We fixed the queue depth bug that caused the restart to be necessary" is resolution.

---

## 3. Root Cause Analysis

### "Human error" is not a root cause

"An engineer deployed the wrong configuration" describes what happened. It does not explain why the system permitted it to happen, why there were no safeguards, or why the engineer had incomplete information. Every incident where human action was a proximate cause must identify at least one contributing systemic factor: inadequate tooling, insufficient visibility, missing safeguards, unclear runbooks, or inadequate review processes.

### Contributing factors must be explicitly assessed

The IR must include an assessment of systemic contributing factors — even if the conclusion is "none identified." Blank root cause sections are not acceptable. "None identified" with an explanation of why is acceptable. Silence is not.

### Do not conflate proximate and root causes

A proximate cause is the immediate trigger. A root cause is the underlying condition that made the proximate cause possible. Both must be identified. Example: proximate cause — "a config push introduced a null pointer exception"; root cause — "the config validation pipeline did not catch null values in required fields."

---

## 4. Blameless Review

### Incidents are learning opportunities, not failure hearings

The purpose of post-incident review is to understand what happened and improve the system — not to assign blame or punish individuals. Organizations that conduct blame-focused reviews create incentives to hide incidents, under-report severity, and avoid declaring incidents at all. This produces worse outcomes.

### Blameless does not mean consequence-free

Blameless means: we assume that everyone involved acted with the information and tools available to them at the time. If an engineer made a mistake, the question is: what information, tooling, or process would have prevented that mistake? If the answer is "none — this was pure negligence," that is a separate conversation that happens outside the IR process.

### Follow-up actions must be tracked to closure

Post-incident review is valueless if the follow-up actions are never completed. Every action item in an IR must have a named owner, a deadline, and a tracking reference. Actions without tracking references are not likely to be completed.

### Recurring incidents are system failures

An incident that recurs — same root cause, same affected component, same pattern — is evidence that the remediation from the prior incident was insufficient. The RHR's incident summary section must identify recurrence patterns. A pattern of recurring incidents with no systemic remediation is a systemic failure, not a run of bad luck.
