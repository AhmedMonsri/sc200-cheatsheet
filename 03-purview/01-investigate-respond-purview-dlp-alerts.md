# Module: Investigate and respond to Microsoft Purview Data Loss Prevention alerts

> Learning path: [Mitigate threats using Microsoft Purview](https://learn.microsoft.com/en-us/training/paths/sc-200-mitigate-threats-using-microsoft-purview/) | Source: [Microsoft Learn module](https://learn.microsoft.com/en-us/training/modules/purview-data-loss-prevention-alerts/)
> Original time: ~76 min (10 units, excl. assessment) | Read time: ~6 min | Last verified: 2026-09-27

## TL;DR

- A DLP alert fires when a user action matches a DLP policy condition **and** the rule is set to create alerts; it means "criteria met", not "malicious".
- The same DLP alerts appear in two portals with different jobs: **Defender XDR** (security: incidents, cross-domain correlation, remediation) and the **Purview Alerts dashboard** (compliance: what matched, content, policy tuning).
- Lifecycle: **Trigger → Notify → Triage → Investigate → Remediate → Tune**.
- The Purview **DLP triage agent** (Security Copilot) ranks alerts by content, exfiltration, and policy risk; the analyst still decides what to act on.
- Close consistently (status, owner, classification, notes) so the queue stays trustworthy; repeated false positives mean the policy needs tuning.

## Exam objectives covered

| Exam domain | Objective (paraphrased) |
|---|---|
| Respond to security incidents | Investigate and remediate threats / compromised entities identified by Microsoft Purview |
| Respond to security incidents | Investigate incidents with agentic AI, incl. embedded Security Copilot (DLP triage agent, Copilot summaries) |
| Respond to security incidents | Investigate Sentinel alerts and incidents (DLP alerts brought into Sentinel via the Defender XDR connector) |
| Perform threat hunting | Create Advanced Hunting queries (Go Hunt from a DLP event) |
| Perform threat hunting | Choose the right table for a KQL query (`CloudAppEvents` for DLP-related activity) |

## Understand data loss prevention (DLP) alerts

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-data-loss-prevention-alerts/understand-data-loss-prevention-alerts

- DLP alert = notification raised when a user's action matches a DLP policy condition (e.g. sharing card numbers, SSNs, financial records).
- Can come from **one event** or from **several small events adding up** over time (no single one violating the policy on its own).
- An alert is not proof of malice: the outcome is either "needs action" or "policy needs tuning".

| Portal | Purpose | Go here when |
|---|---|---|
| Microsoft Defender XDR | Security investigation; alerts consolidated into incidents | The DLP alert may be part of a wider threat (e.g. exfiltration plus AV tampering by the same user) |
| Microsoft Purview | Compliance; focus on the policy and rules | You need to see what matched and why, or fix a false positive by adjusting the policy |

- ⚠️ Exam tip: both portals show the **same** DLP alerts. Correlation with other signals → Defender XDR; policy match and tuning → Purview.

## Understand the DLP alert lifecycle

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-data-loss-prevention-alerts/data-loss-prevention-alert-lifecycle

| Stage | What happens |
|---|---|
| Trigger | User action matches a policy condition (external sharing, copy to removable media, upload to unsanctioned cloud app); policy can block, warn, and (only if configured) create an alert |
| Notify | Alert appears in the Defender portal (grouped into incidents) and the Purview alerts dashboard; optional email notifications; Activity explorer for detail; APIs to export activity data for long-term storage/reporting |
| Triage | Decide false positive vs. needs investigation; set priority; assign an owner |
| Investigate | Gather evidence, review activity logs, plan remediation |
| Remediate | Alert owner chooses and applies actions |
| Tune | Check whether the policy worked as intended and adjust it |

- Filter the Defender incident queue to DLP: **Add filter → Service/detection sources → Microsoft Data Loss Prevention**.
- If **Insider Risk Management** signals are shared with Defender, the user's insider risk severity appears next to DLP alerts (helps prioritize).
- Security Copilot is embedded (in some tenants) in the DLP Alerts dashboard and in Data Security Posture Management (preview).

| Question you need answered | Start in |
|---|---|
| Is this part of a broader pattern across endpoints, email, identity, cloud apps? | Microsoft Defender |
| What exactly matched, what's in the file, does the policy need tuning? | Purview alerts dashboard |
| Compare/filter user actions over time without opening alerts | Activity explorer |
| Inspect the actual content that triggered the match | Content explorer |
| What else has this user done (exfiltration, bypassed warnings)? | User activity summary (IRM data sharing on + user in IRM policy scope; up to **120 days**) |

- Common remediation: mark informational / no action; talk to the user; block sharing or revoke access; remove file or apply sensitivity label; reset password, disable account, isolate device.
- Directly in Defender: remove/quarantine file, revoke sharing, disable account, reset password, download/delete email, Advanced Hunting for related events.
- Tuning levers: condition sensitivity, policy scope (users, locations, groups), notification settings, whether low-risk actions should alert at all.

## Configure DLP policies to generate alerts

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-data-loss-prevention-alerts/configure-data-loss-prevention-alert-generation

- Path: Purview portal → **Solutions > Data Loss Prevention > Policies** → choose locations (Exchange, SharePoint, devices…) → create/edit a **rule** → after conditions and actions, configure **incident reports and alerting** → deploy **on**, in **simulation mode**, or **off**.
- Alert settings are part of the **rule** configuration.

| Alert type | Fires | Best for | Licensing |
|---|---|---|---|
| Single-event | On every rule match | Low-volume, high-sensitivity events | E1, F1, G1, E3, G3 |
| Aggregate-event | Only when a threshold is met: **number of matches** (e.g. 10 in 60 min) or **volume of data** (e.g. > 1 MB) | High-volume environments; reduces alert fatigue | E5/G5, or E1/E3/G1/G3 + add-on: Office 365 ATP Plan 2, Microsoft Purview Suite (formerly M365 E5 Compliance), or M365 eDiscovery and Audit |

| Aggregation window | License |
|---|---|
| 1 minute | E5 or add-on |
| 15 minutes | E3/G3 or lower without add-on |

- Matches on the **same item in the same location** within the window are grouped into one alert.

| Need | Role / role group |
|---|---|
| Configure or view DLP alerts | Compliance Administrator, Information Protection Admin, Security Operator, Security Reader, Information Protection Investigator |
| Access the DLP alert management dashboard | **Manage alerts** role + membership in **DLP Compliance Management** or **View-Only DLP Compliance Management** |
| View matched content / use Content explorer | **Content Explorer Content Viewer** (in addition) |

- Alert emails, incident reports, and user notifications go out **once per document** per match; re-matches in the window join the existing alert.
- A new or updated policy can take **up to 3 hours** to start generating alerts.
- **Endpoint DLP** and **Teams DLP** alerts also appear in the DLP alerts dashboard.
- **User-and-rule-based alert aggregation** (preview): groups single-event alerts when the same user triggers the same rule within a configurable **15–60 min** window (fewer alerts, more events each); check under **Data Loss Prevention > Settings**.
- ⚠️ Exam tip: aggregate (threshold) alerts need **E5-level** licensing or an add-on; single-event alerts work from E1/E3.

## Investigate DLP alerts in Microsoft Purview

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-data-loss-prevention-alerts/investigate-alerts-purview

- Use when the alert has no other security signals and you need what matched, why, and whether to tune.
- Location: Purview portal → **Data loss prevention > Alerts**.
- How long alerts stay visible = your **audit log retention** policy (extend in Purview Audit).
- Workloads: Exchange, SharePoint, OneDrive, Teams chats/channels, devices, instances, on-premises repositories, Fabric and Power BI.

| Step | What you do |
|---|---|
| 1. Filter | Filter by policy name, severity, user, status, detection time; **Customize columns** |
| 2. Details tab (default) | Alert ID, status, severity, detection time, matched policy and rule, workload |
| 3. User activity summary | Only with IRM **data sharing** on and user **in scope of an IRM policy**; exfiltration activity over the past **120 days** |
| 4. Events | **View events** / Events tab: file name, location, size, timestamp; whether the user **overrode a policy tip**; which rule matched; **Actions** menu |
| 5. Complete | Summarize in **Overview**, add comments, assign owner, set Status to **Resolved** |

- Event **Actions**: download matched content (role required), apply/remove sensitivity label, unshare or delete file, send notification email, **copy event link**.
- **Summarize with Copilot**: triggered policy/rule, file and access path, user identity + insider risk level, suggested follow-ups; copy, regenerate, or open in standalone.
- Event link is for people outside security/compliance role groups: **view only**, grants no edit or action rights.
- Alert data: matched content (names, labels, sizes), user actions (send, upload, copy), policy match (rule, condition, **SIT**); visible in Events, Content explorer, Activity explorer depending on role.
- Exchange DLP: email download can fail if the message was deleted by the internal sender (sent externally), the internal recipient (received externally), or both.
- ⚠️ Exam tip: no **User activity summary** tab → check IRM data sharing and the user's IRM policy scope.

## Investigate DLP alerts in Microsoft Defender XDR

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-data-loss-prevention-alerts/investigate-alerts-defender

- Why Defender: auto-correlates DLP alerts into **incidents**, extends across endpoints, email, cloud apps, identities, and offers response actions.
- Licensing: most capabilities need **Microsoft 365 E5** or equivalent compliance licensing; RBAC: Defender or compliance roles to view/act.
- Queue: Defender portal → **Incidents & alerts > Incidents** → filter **Service/detection sources = Microsoft Data Loss Prevention**.
- Incident history kept **6 months** (search for resolved/recurring incidents for the same user).

| Alert page element | Shows |
|---|---|
| Alert story | What triggered the policy match |
| Related events | User activity such as downloads, shares, overrides |
| Sensitive info types tab | SITs detected |
| Source tab | The file itself (if permissions allow) |
| Copilot summary | Title, severity, policy/rule, file and access path, user and activities |

| Scope | Response actions |
|---|---|
| Exchange alerts | Download email |
| SharePoint / OneDrive files | Apply sensitivity label, Unshare, Delete, Download |
| General | Apply retention label, Send email notification, Withdraw feedback (alert marked incorrectly) |
| User card | Reset password, disable account |
| Device card | Isolate or manage the device |

- Endpoint DLP file content requires **evidence collection for file activities on devices**; without it you see only metadata and match details.
- **Manage incident**: severity, tags, assignee, status (e.g. In Progress, Resolved), **classification** (True / False Positive) + reason (e.g. Malicious user activity, Security testing).
- Advanced hunting table: **`CloudAppEvents`** (Microsoft 365 audit logs: Exchange, SharePoint, OneDrive, devices).
- Start hunting from **Advanced hunting** (built-in queries) or **Go Hunt** on an event; contextual queries: File shared with, File activities, Site activity, User DLP violations (last 30 days).
- **Sentinel** adds cross-platform investigation, custom correlation rules, and SOAR automation.
- Sentinel setup: **Defender XDR connector** (DLP alerts and incidents) → ingest `CloudAppEvents` → correlate with KQL.

```kusto
// Sentinel: CloudAppEvents activity linked to one DLP alert
let Alert = SecurityAlert
    | where TimeGenerated > ago(30d)
    | where SystemAlertId == "<alert-id>";
CloudAppEvents
| extend correlationId = tostring(parse_json(tostring(RawEventData.Data)).cid)
| join kind=inner Alert on $left.correlationId == $right.AlertType
```

- ⚠️ Exam tip: DLP alerts reach Sentinel through the **Microsoft Defender XDR connector**; related activity lives in **`CloudAppEvents`**.

## Investigate DLP alerts with Security Copilot and AI agents

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-data-loss-prevention-alerts/investigate-alerts-security-copilot-agents

- **DLP triage agent** (Purview, powered by Security Copilot): evaluates and ranks DLP alerts; the analyst still decides.
- Custom instructions in plain language → converted to logical conditions, applied on every run; supports manual reruns/adjustments.
- View: **Data Loss Prevention > Alerts** → switch to **Alert triage agent (preview)** view.

| Category | Meaning |
|---|---|
| Needs attention | Highest risk; review first |
| Less urgent | Lower severity or likelihood of data loss |
| Not categorized | Agent couldn't evaluate (e.g. unsupported policy type) |

| Risk factor | Based on |
|---|---|
| Content risk | SITs, trainable classifiers, sensitivity labels in files |
| Exfiltration risk | Data leaving approved channels (external sharing, downloads) |
| Policy risk | Rule mode and actions, incl. label removed or downgraded |

- Deployment: provision **Security Compute Units (SCUs)**, enable the **Purview plugin** in Security Copilot, assign an **agent identity**.
- Roles: Information Protection Analyst or Investigator, Purview Agent Analysis, Security Copilot Contributor; device alerts also need Data Classification Content Viewer and Content Downloader.

| Run mode | Behavior |
|---|---|
| Automatic | Fixed schedule; lookback **24 hours to 30 days**; triages existing alerts in the window plus new ones |
| Manual | One alert at a time; can rerun when conditions change |

- Review flow: **Needs attention** first → **Agent summary** pane (categorization, triage notes) → **Summarize** for more context → View details, assign, notify.
- Copilot can explain the priority, highlight sensitive data and exfiltration risk, correlate across SharePoint, OneDrive, Exchange, Teams, and suggest next actions.
- Supported: Exchange, SharePoint, OneDrive, Teams, devices (devices need evidence collection for file activities).
- Not supported: alerts triggered only by **custom SITs** or **custom trainable classifiers**; **simulation-mode** policies; **administrative unit** scoping.
- Files **> 2 MB** aren't analyzed; alerts with **> 10 files** → only the 10 most relevant are analyzed.
- Not fully triaged → continue in the DLP Alerts dashboard or Defender XDR.
- ⚠️ Exam tip: the agent only triages alerts from **active** DLP policies, never simulation mode.

## Respond to DLP alerts

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-data-loss-prevention-alerts/respond-data-loss-prevention-alerts

| Action | Purview Alerts dashboard | Defender XDR |
|---|---|---|
| Track progress | Alert status (e.g. Investigating, Resolved) | Incident status |
| Ownership | Assign to a reviewer | Assign to a team member |
| Documentation | Comments | Notes |
| Classification | **None** (explain in a comment) | True Positive / False Positive + reason |
| Remediation | Event actions (label, unshare, delete, notify) | Disable account, remove file access, apply sensitivity/retention label |
| Extra context | User activity summary (IRM), read-only share link | Correlation with other security signals |

- False positive in Defender → classify **False Positive** + reason (e.g. Inaccurate alert, Security testing); shows in Defender reporting to track policy noise.
- False positive in Purview → set **Resolved** + comment with rationale.
- Recurring legitimate activity → adjust conditions, add an exception, or narrow scope.
- **Resolved ≠ suppressed**: the same activity still creates new alerts.
- Resolved incidents stay visible in Defender up to **6 months**; Purview resolved alerts follow audit log retention.
- ⚠️ Exam tip: Purview has **no classification field**; True/False Positive classification is a Defender XDR incident feature.

## Exercise - Investigate a DLP alert and related incident

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-data-loss-prevention-alerts/exercise-investigate-alert

- Interactive guide: prioritize high-risk alerts with the Triage Agent and read its Agent summary (sensitivity, policy, exfiltration risk).
- Then finds the same alert in Defender XDR and reviews correlated incidents and alert patterns for a single user.

## Key terms

| Term | Meaning |
|---|---|
| DLP | Data Loss Prevention: Purview policies that detect and act on risky handling of sensitive data |
| SIT | Sensitive information type (e.g. credit card number pattern) matched by a DLP rule |
| Single-event alert | Alert raised on every rule match |
| Aggregate-event alert | Alert raised only when a count or volume threshold is met (E5-level) |
| Simulation mode | Policy deployment state that evaluates matches without enforcing; excluded from the triage agent |
| Activity explorer | Purview tool to filter and compare user actions over time |
| Content explorer | Purview tool to inspect the content that matched (needs Content Explorer Content Viewer) |
| User activity summary | IRM-powered tab showing up to 120 days of a user's risky/exfiltration activity |
| DLP triage agent | Security Copilot agent in Purview that categorizes and prioritizes DLP alerts |
| SCU | Security Compute Unit: Security Copilot capacity required to run the agent |
| Evidence collection for file activities on devices | Endpoint DLP setting required to view/download files behind device alerts |
| `CloudAppEvents` | Advanced hunting table with Microsoft 365 audit activity used in DLP investigations |

## Exam traps

- Defender XDR vs Purview: same alerts; Defender = correlation, incidents, classification; Purview = policy match, content, tuning.
- Resolve vs suppress: resolving closes one alert; it doesn't stop future alerts for the same pattern (tune the policy instead).
- Classification: True/False Positive exists in **Defender** only; in Purview document it in a comment.
- Retention: Defender incidents **6 months**; Purview alerts follow **audit log retention**; user activity summary **120 days**.
- Aggregation window: **1 min** (E5/add-on) vs **15 min** (E3/G3 without add-on); preview user-and-rule aggregation **15–60 min**.
- New/updated policy alerts: allow **up to 3 hours**.
- Endpoint DLP file content needs **evidence collection**; otherwise only metadata.
- Triage agent exclusions: simulation mode, custom SITs/classifiers only, admin units, files > 2 MB.

## Top 3 takeaways

1. DLP alerts show in both Defender XDR (security correlation and incident response) and Purview (policy match and tuning); pick the portal by the question.
2. Alert behavior is set in the DLP rule: single-event vs aggregate (E5-level), aggregation window by license, up to 3 hours to take effect.
3. Close every alert consistently (owner, status, notes, classification in Defender) and tune the policy for recurring false positives; the DLP triage agent helps prioritize but excludes simulation-mode and custom-SIT-only alerts.
