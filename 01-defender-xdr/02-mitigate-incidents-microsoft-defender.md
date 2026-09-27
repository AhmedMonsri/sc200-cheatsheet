# Module: Mitigate incidents using Microsoft Defender

> Learning path: [Mitigate threats using Microsoft Defender XDR](https://learn.microsoft.com/en-us/training/paths/sc-200-mitigate-threats-using-microsoft-365-defender/) | Source: [Microsoft Learn module](https://learn.microsoft.com/en-us/training/modules/mitigate-incidents-microsoft-365-defender/)
> Original time: ~79 min (14 units, excl. assessment) | Read time: ~12 min | Last verified: 2026-09-27

## TL;DR

- The Microsoft Defender portal (`security.microsoft.com`) is the single console where alerts from MDE, MDO, MDI, and MDCA are correlated into **incidents** you triage, investigate, and resolve.
- Core analyst loop: **incident queue → incident page (alerts, entities, evidence, graph) → alert story → response actions → Action center**.
- Automation: **AIR** investigates and remediates automatically (or after approval, depending on automation level); every action lands in the **Action center**, where most can be undone.
- Proactive side: **advanced hunting** (KQL over 30 days of raw data) feeds **custom detection rules**; the **hunting graph** and **blast radius** add graph-based views via Sentinel graph.
- Also covered: Unified RBAC, Entra sign-in investigation, Secure Score, threat analytics with the Security Copilot TI Briefing Agent, reports, and email notifications.

## Exam objectives covered

| Exam domain | Objective (paraphrased) |
|---|---|
| Manage a security operations environment | Email notifications in Defender XDR (incidents, threat analytics) |
| Manage a security operations environment | Alert tuning via suppression rules |
| Manage a security operations environment | Automated investigation and response (AIR) |
| Manage a security operations environment | Defender for Endpoint permissions and automation levels |
| Manage a security operations environment | Custom detection rules from advanced hunting (create and manage) |
| Respond to security incidents | Investigate complex attacks (multi-stage, multi-domain, lateral movement) |
| Respond to security incidents | Evidence and entity investigation |
| Respond to security incidents | Compromised identities (Entra sign-in logs) |
| Respond to security incidents | Investigate with agentic AI (embedded Security Copilot agent) |
| Perform threat hunting | Choose the right table; build advanced hunting queries |
| Perform threat hunting | Interpret threat analytics |
| Perform threat hunting | Hunting graphs incl. blast radius; entity relationships via Sentinel graph |

## Use the Microsoft Defender portal

> Unit: https://learn.microsoft.com/en-us/training/modules/mitigate-incidents-microsoft-365-defender/2-use-microsoft-security-center-portal

- One portal for protection, detection, investigation, and response across email, collaboration, identity, devices, and apps.
- Home page cards depend on the user's **role** (RBAC-driven).
- Workloads in the portal: MDO, MDE, Defender XDR, MDCA, MDI, **Defender Vulnerability Management (MDVM)**, **Defender for IoT** (OT environments), and **Microsoft Sentinel** integration.
- Sentinel integration streams Defender XDR incidents and advanced hunting events into Sentinel and keeps incidents synced between the Azure and Defender portals.
- "More resources" links out to: Purview portal, Entra ID, Entra ID Protection, Azure Information Protection, Defender for Cloud.

**Defender XDR Unified RBAC**

- Unified RBAC permissions map to the old per-product permissions; once activated, imported roles **replace** the individual product RBAC models.
- Default for **new** tenants: MDE from **16 Feb 2025**, MDI from **2 Mar 2025** (no export from the old model); existing tenants keep their current setup.
- Permission groups: **Security operations** (data, alerts, response, live response, email quarantine/advanced actions), **Security posture** (vulnerability management, Secure Score), **Authorization and settings**.
- Not in Unified RBAC: **Intune endpoint security settings** (managed in the Intune admin center) and **App governance** (uses Entra roles).
- After activation, **Security Reader** and **Global Reader** can access MDE data.
- MDI scoped deployment configured in MDCA doesn't carry over; grant *Security data basics (read)* explicitly.

| Entra role | Default Unified RBAC access (highlights) |
|---|---|
| Global admin / Security admin | Full: data, alerts, response, live response, Secure Score manage, settings, authorization |
| Security operator | Data, alerts, response, live response, file collection; no authorization management |
| Security reader / Global reader | Read data and Secure Score |
| Exchange / SharePoint admin | Secure Score read + manage |
| Compliance admin | MDO only: data read + alerts manage |

- ⚠️ Exam tip: Microsoft recommends least privilege; Global Administrator is for emergencies only.

## Manage incidents

> Unit: https://learn.microsoft.com/en-us/training/modules/mitigate-incidents-microsoft-365-defender/3-manage-incidents

- **Incident** = a set of correlated alerts telling one attack story across devices, users, and mailboxes (entry point, tactics, scope, affected entities).
- Defender XDR also raises **unique alerts** only visible through cross-product correlation.
- Incident queue shows the **last 30 days** by default, newest first; columns are customizable.
- **Automatic incident naming** uses alert attributes (endpoints/users affected, detection sources, categories); you can rename.
- Useful filters: status, severity, assignment, service sources, multiple service source, multiple category, categories, tags, entities, device group, OS platform, classification, automated investigation state, associated threat.
- **Data sensitivity** filter works only when **Microsoft Purview Information Protection** is on.
- Management actions: rename, assign, set status, classify + determination, tag, comment (logged in *Comments and history*).
- **Assign to me** on an incident takes ownership of the incident **and all its alerts**.
- Incident status: **Active / Resolved**; resolving an incident **closes all its open alerts**.
- Alerts can be moved between incidents from the **Alerts** tab to enlarge or shrink an incident.
- ⚠️ Exam tip: tags are the way to group incidents with common traits and later filter the queue on them.

## Investigate incidents

> Unit: https://learn.microsoft.com/en-us/training/modules/mitigate-incidents-microsoft-365-defender/4-investigate-incidents

| Incident page tab | What it shows |
|---|---|
| Overview | Attack categories (kill chain, MITRE ATT&CK aligned), scope (top impacted assets), alerts timeline, evidence summary |
| Alerts | All alerts, chronological by default, with source and link reason |
| Devices | Devices with related alerts → Device page |
| Users | Related users → user's **MDCA** page |
| Mailboxes / Apps | Related mailboxes and apps |
| Investigations | Automated investigations; pending actions await approval |
| Evidence and Responses | Each entity with verdict (**Malicious / Suspicious / Clean**) and remediation status |
| Graph | Visual attack story: entry point, IoCs, device relationships, file prevalence |

**Blast radius analysis (preview)**

- Extends the incident graph with **post-breach** impact and **pre-breach** potential propagation toward critical targets, in one view.
- **Replaces attack path analysis.**
- Requires onboarding to the **Microsoft Sentinel data lake** (uses Sentinel graph); without it the feature doesn't appear.
- Needs Unified RBAC **Exposure management (read)** or higher; results are scoped to the viewer's RBAC.
- Launch: select a node → *View blast radius*; act from nodes (isolate device, disable user).
- Limits: up to **7 hops** overall (typically 5 cloud/on-prem, 3 hybrid); paths are possible, not guaranteed; data can lag.
- ⚠️ Exam tip: no Sentinel data lake = no blast radius.

## Manage and investigate alerts

> Unit: https://learn.microsoft.com/en-us/training/modules/mitigate-incidents-microsoft-365-defender/5-manage-investigate-alerts

| Severity | Typical meaning |
|---|---|
| High (red) | APT-like: credential theft tools, ransomware, sensor tampering, human adversary |
| Medium (orange) | EDR post-breach behaviors that could be part of an APT |
| Low (yellow) | Prevalent malware, hack tools, possibly internal testing |
| Informational (grey) | Not harmful; awareness only |

- **Defender AV severity** = absolute severity of the malware to one device; **MDE alert severity** = actual behavior risk plus **risk to the organization**.
- Example: AV-prevented, no infection → Informational; malware blocked while executing → Low; org-level threat → Medium/High even if blocked.
- **Categories** align with MITRE ATT&CK tactics, plus extras not in ATT&CK (e.g. Unwanted software, Malware, Ransomware, Exploit, Suspicious activity).
- Alert status: **New / In Progress / Resolved**.
- Classification: true alert / false alert / not set; **determination** adds detail to a true positive and improves alert quality.
- Alerts can be linked to an existing incident or spun into a new one.
- **Suppression rules** (MDE): created from an existing alert, can be disabled/re-enabled, scope = **this device** or **my organization**.
- Suppression applies **only to new alerts after creation**, never to alerts already in the queue.
- **Alert story**: why the alert fired, events before/after, related entities; selecting an entity switches the details pane and exposes its actions.
- ⚠️ Exam tip: false positive from a line-of-business app → classify as false alert **and** create a suppression rule.

## Manage automated investigations

> Unit: https://learn.microsoft.com/en-us/training/modules/mitigate-incidents-microsoft-365-defender/6-manage-automated-investigations

- **AIR** (MDE) mimics analyst steps, cuts alert volume, and can remediate automatically.
- Trigger: an alert fires → a security playbook runs → an automated investigation may start.
- Verdicts: **Malicious / Suspicious / No threats found**.
- Investigation tabs: Alerts, Devices, Evidence, Entities, Log, **Pending actions** (approve/reject).
- Scope expansion: new alerts on the device join the running investigation; devices with the same incriminated entity are added.
- Expansion to **10 or more devices** from one entity **requires approval**.
- Remediation examples: quarantine file, stop service, remove scheduled task; PUA protection settings also affect it.

| Automation level | Behavior |
|---|---|
| Full (recommended) | Remediates malicious artifacts automatically; see History tab |
| Semi – any remediation | Every action needs approval (Pending tab) |
| Semi – core folders | Approval needed for OS folders (e.g. `\Windows\`); others automatic |
| Semi – non-temp folders | Approval needed outside temp folders; temp folders (e.g. `\Downloads`, `\AppData\Local\Temp`) automatic |
| No automated response | AIR doesn't run; not recommended |

- ⚠️ Exam tip: device groups with an existing automation level keep it when new defaults roll out.

## Use the action center

> Unit: https://learn.microsoft.com/en-us/training/modules/mitigate-incidents-microsoft-365-defender/7-use-action-center

- Unified **Action center** = pending and completed remediation actions across **MDE and MDO** (devices, email, identities).
- **Pending** tab: actions awaiting approve/reject (shown only when pending items exist); bulk approval supported.
- **History** tab: audit log of AIR actions, approved actions, **live response** commands, and **Defender AV** actions.
- **Undo** works for actions from AIR, Defender AV, and manual response: isolate device, restrict code execution, quarantine file, remove registry key, stop service, disable driver, remove scheduled task.
- Un-quarantine a file on many devices: History → quarantined file → *Apply to X more instances* → Undo.
- Action source values: manual/automated device action, manual/automated email action, advanced hunting action, Explorer action, manual live response action, live response action (via **MDE APIs**).

**Submissions (MDO)**

- Admins submit emails, URLs, and attachments to Microsoft; results include email authentication, policy hits, payload detonation, and human grader review.
- Detonation and grader analysis don't run in tenants where data can't leave the boundary.
- Roles: **Security Administrator** or **Security Reader**.
- Limits: messages up to **30 days** old (if still in mailbox); max **150 submissions / 15 min**; same item **3 / 24 h** and **1 / 15 min**.
- Email submission input: **network message ID** (GUID) or `.eml`/`.msg` upload; reason = false positive or false negative (phish, malware, spam).
- If malware filtering replaced attachments, submit the original from **quarantine**.
- User-reported messages: *Mark as and notify* sends the reporter an email; custom-mailbox-only reports show empty results.
- Custom-mailbox reports can be converted to admin submissions (clean, phishing, malware, spam, or trigger investigation); users can't undo a submission.
- ⚠️ Exam tip: grader feedback can take up to a day; override results appear in minutes.

## Explore advanced hunting

> Unit: https://learn.microsoft.com/en-us/training/modules/mitigate-incidents-microsoft-365-defender/8-explore-advanced-hunting

- KQL-based hunting over **up to 30 days of raw data** from MDE, MDO, MDCA, and MDI; times are in **UTC**.
- Event/activity data arrives almost immediately; **entity data** (users, devices) refreshes every **15 min**, full consolidation every **24 h**.
- Schema reference per table: description, columns, **ActionType** values (event tables only), sample queries.

| Need | Table |
|---|---|
| Alerts and their evidence | `AlertInfo`, `AlertEvidence` |
| Process / file / registry / network / logon on devices | `DeviceProcessEvents`, `DeviceFileEvents`, `DeviceRegistryEvents`, `DeviceNetworkEvents`, `DeviceLogonEvents` |
| DLL loads | `DeviceImageLoadEvents` |
| AV and exploit protection events | `DeviceEvents` |
| Device inventory / network config | `DeviceInfo`, `DeviceNetworkInfo` |
| Vulnerabilities and software | `DeviceTvm*` tables |
| Email delivery / post-delivery / attachments / URLs | `EmailEvents`, `EmailPostDeliveryEvents`, `EmailAttachmentInfo`, `EmailUrlInfo` |
| On-prem AD events and LDAP queries | `IdentityDirectoryEvents`, `IdentityQueryEvents` |
| AD and online authentication | `IdentityLogonEvents` |
| Account info (incl. Entra ID) | `IdentityInfo` |
| Office 365 and cloud app activity | `CloudAppEvents` |

**Custom detection rules**

- Built from advanced hunting queries; raise alerts and run response actions on a schedule.
- Query must return **`Timestamp`, `DeviceId`, `ReportId`**; after `summarize`, use `arg_max` to keep them.
- Max **100 alerts per run**.
- On save, the rule runs once over the **past 30 days**, then on its schedule.

| Frequency | Lookback |
|---|---|
| Every 24 h | 30 days |
| Every 12 h | 48 h |
| Every 3 h | 12 h |
| Every 1 h | 4 h |
| Continuous (NRT) | Events as processed |

- Alert details: name, frequency, title, severity, category, MITRE techniques (not available for malware, ransomware, suspicious activity, unwanted software), description, recommended actions.
- Impacted entities: one column per entity type, only columns the query returns.
- Device actions (`DeviceId`): isolate, collect investigation package, AV scan, initiate investigation.
- File actions (`SHA1` / `InitiatingProcessSHA1`): allow/block (custom indicator, own device-group scope) or quarantine.
- Rule scope: all devices or specific device groups; only in-scope data is queried and acted on.

```kusto
DeviceEvents
| where Timestamp > ago(1d)
| where ActionType == "AntivirusDetection"
| summarize (Timestamp, ReportId) = arg_max(Timestamp, ReportId), Detections = count() by DeviceId
| where Detections > 5
```

**Hunting graph (preview)**

- Sentinel graph integrated into advanced hunting: predefined threat scenarios rendered as **nodes** (entities) and **edges** (relationships).
- Use for lateral-movement paths, exposure of sensitive assets (Key Vault, storage, SQL, Kubernetes, Azure DevOps), and choke points.
- Prerequisites: Entra role for advanced hunting, **Sentinel data lake** access, at least read in **Security Exposure Management**.
- Launch: Investigation & response > Hunting > Advanced hunting → hunting graph icon or *Create new > Hunting graph*.
- Filters: shortest paths, source/target properties (critical, vulnerable, sensitive data, internet-exposed), edge types.
- Direction matters in "Paths between two entities" (start → end).
- ⚠️ Exam tip: graph to scope visually, then KQL/custom detections to validate and automate.

## Investigate Microsoft Entra sign-in logs

> Unit: https://learn.microsoft.com/en-us/training/modules/mitigate-incidents-microsoft-365-defender/9-investigate-azure-ad-sign-in-logs

| Where | Table |
|---|---|
| Defender XDR advanced hunting | `AADSignInEventsBeta` → now `EntraIdSignInEvents` |
| Entra ID Log Analytics | `SigninLogs` |

- Both let you check which Conditional Access policies were evaluated at sign-in.
- Portal equivalent: Entra ID → Monitoring → **Sign-in logs** (Date, User, Application, Status, Conditional Access).
- Docs: `AADSignInEventsBeta` is deprecated; queries migrate automatically to `EntraIdSignInEvents` on **19 Oct 2026**; requires **Entra ID P2**.

```kusto
EntraIdSignInEvents
| where Timestamp > ago(1d)
| where ErrorCode != 0
| project Timestamp, AccountUpn, Application, ErrorCode
```

- ⚠️ Exam tip: exam version from 21 Oct 2026 postdates the table migration; expect either name.

## Understand Microsoft Secure Score

> Unit: https://learn.microsoft.com/en-us/training/modules/mitigate-incidents-microsoft-365-defender/10-understand-microsoft-secure-score

- Secure Score view combines **Microsoft Secure Score** (posture; higher = more recommended actions done) and **Cloud Secure Score** (risk-based cloud posture).
- Part of **Exposure Management** in the Defender portal.
- Covers Microsoft products (MDO, Exchange Online, Entra ID, MDE, MDI, MDCA, Purview Information Protection, Teams, SharePoint Online, App governance) and some third-party SaaS (e.g. Salesforce, ServiceNow, Okta, GitHub, Zoom).
- **Recommended actions** statuses: to address, planned, risk accepted, resolved through third party, resolved through alternate mitigation, completed.
- ⚠️ Exam tip: third-party or alternate mitigations can be credited to the score.

## Analyze threat analytics with the Security Copilot Threat Intelligence Briefing Agent

> Unit: https://learn.microsoft.com/en-us/training/modules/mitigate-incidents-microsoft-365-defender/11-analyze-threat-analytics

- Embedded Security Copilot agent that builds a threat briefing from recent threat actor activity plus your vulnerability context; it chooses its next analysis step based on prior results.
- Location: **Threat intelligence > Threat analytics** (banner at the top of the dashboard).
- Prerequisites: Security Copilot; plugins **Microsoft Threat Intelligence** and **Microsoft Threat Intelligence agents** (optional: **Defender EASM**).
- Permissions: MDVM data access, **Security Reader** for threat analytics, **Security Admin** for onboarding/configuration.
- Settings: *Manage agent* or System > Settings > Microsoft Defender XDR > Threat Intelligence Briefing Agent.
- Output: copy or download as markdown; history in Security Copilot **Activity**; *View activity* shows agent reasoning; thumbs up/down for feedback.

## Analyze reports

> Unit: https://learn.microsoft.com/en-us/training/modules/mitigate-incidents-microsoft-365-defender/12-analyze-reports

| Area | Reports |
|---|---|
| General | Security report (trends, protection status) |
| Endpoints | Threat protection, device health and compliance, vulnerable devices, web protection, firewall, device control (media usage), ASR rules |
| Email & collaboration | Email & collaboration reports, manage schedules, reports for download, Exchange mail flow (deep link to EAC) |

- ⚠️ Exam tip: ASR rules report shows detections, misconfigurations, and suggested exclusions.

## Configure the Microsoft Defender portal

> Unit: https://learn.microsoft.com/en-us/training/modules/mitigate-incidents-microsoft-365-defender/13-configure-microsoft-365-defender-portal

- Main Defender XDR setting: **email notifications**, at Settings > Microsoft Defender XDR > Email notifications.

| Notification type | Fires when | Criteria |
|---|---|---|
| Incidents | New incident created | Device and alert criteria |
| Threat Analytics | New threat analytics report | Threat analytics criteria |

- Rule wizard: name/description → criteria → recipients → Create rule.

## Key terms

| Term | Meaning |
|---|---|
| Incident | Group of correlated alerts forming one attack story |
| Alert story | Alert view showing trigger, surrounding events, and related entities |
| AIR | Automated investigation and remediation in MDE |
| Action center | Unified list of pending and completed remediation actions (MDE + MDO) |
| Suppression rule | Hides future matching MDE alerts on one device or org-wide |
| Custom detection rule | Scheduled advanced hunting query that raises alerts and takes actions |
| NRT | Near real-time (continuous) custom detection frequency |
| Unified RBAC | Single Defender XDR permission model replacing per-product RBAC |
| Blast radius analysis | Incident graph view of current and potential breach spread (Sentinel graph) |
| Hunting graph | Graph-based advanced hunting with predefined scenarios |
| Secure Score | Posture measurement in Exposure Management |
| TI Briefing Agent | Embedded Security Copilot agent producing threat briefings in threat analytics |

## Exam traps

- Incident vs alert status: incidents are **Active/Resolved**; alerts are **New/In Progress/Resolved**.
- Suppression rules affect **future** alerts only; existing queue items stay.
- AV severity = threat to the device; MDE alert severity = risk to the **organization**.
- AIR scope expansion needs approval at **10+ devices**.
- Semi-automation "core folders" vs "non-temp folders": approval is needed **in** core folders vs **outside** temp folders.
- Custom detection required columns: `Timestamp`, `DeviceId`, `ReportId`; limit **100 alerts per run**.
- 24 h rule frequency looks back **30 days**, not 24 h.
- Blast radius and hunting graph both need the **Sentinel data lake**.
- `AADSignInEventsBeta` (advanced hunting) vs `SigninLogs` (Log Analytics).
- Email notification types in this module: **Incidents** and **Threat Analytics** only.

## Top 3 takeaways

1. Incidents correlate cross-product alerts; manage them (assign, classify, tag, resolve) and investigate through the incident page, alert story, and graph.
2. AIR automation levels decide auto vs approved remediation; the Action center tracks and can undo actions.
3. Advanced hunting (30 days, UTC, KQL) powers custom detection rules; the hunting graph and blast radius add Sentinel graph–based path analysis.
