# Module: Investigate insider risk alerts and related activity

> Learning path: [Mitigate threats using Microsoft Purview](https://learn.microsoft.com/en-us/training/paths/sc-200-mitigate-threats-using-microsoft-purview/) | Source: [Microsoft Learn module](https://learn.microsoft.com/en-us/training/modules/purview-insider-risk-investigate-alerts/)
> Original time: ~82 min (12 units, excl. assessment) | Read time: ~5 min | Last verified: 2026-09-27

## TL;DR

- Insider Risk Management (IRM) alerts are the end of a fixed chain: settings → policy → triggering event → scored activity → alert when the score passes the policy threshold.
- Analysts triage in the **Alerts** dashboard, dig into context with three tabs (**All risk factors**, **Activity explorer**, **User activity**), and escalate serious findings to a **case**.
- The Security Copilot–powered **insider risk triage agent** pre-sorts alerts so analysts review the riskiest first.
- IRM alerts flow into **Defender XDR** incidents and advanced hunting tables, with status/classification synced both ways.

## Exam objectives covered

| Exam domain | Objective (paraphrased) |
|---|---|
| Respond to security incidents | Investigate and remediate threats / compromised entities identified by Microsoft Purview (Insider Risk Management) |
| Respond to security incidents | Investigate with agentic AI, incl. embedded Security Copilot (triage agent, Copilot alert summaries) |
| Perform threat hunting | Advanced Hunting queries / choose the right table (`DataSecurityEvents`, `DataSecurityBehaviors`) |

## Understand insider risk alerts and investigations

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-insider-risk-investigate-alerts/understand-insider-risk-alerts

| Step | What happens |
|---|---|
| 1. Settings configured | Indicators to monitor, sensitive domains, privacy options |
| 2. Policy created | Defines who is in scope, what to detect, which events start active monitoring |
| 3. Triggering event | Activates the policy for one user (e.g. resignation date set, risky site visited, exfiltration detected) |
| 4. Activity scored | User actions get risk scores based on activity type, thresholds, and user history |
| 5. Alert generated | Only when the user's risk score exceeds the policy threshold |

- Activity alone doesn't guarantee an alert: a download may score low unless other risk factors combine with it.
- ⚠️ Exam tip: no triggering event = no active scoring for that user; the trigger comes **before** scoring.

## Manage alert volume in insider risk management

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-insider-risk-investigate-alerts/manage-alert-volume

| Too few alerts → | Too many alerts → |
|---|---|
| Enable more indicators (**Policy indicators** settings) | Enable **Analytics** (Settings > Analytics) to find high-risk areas |
| Add users/groups to policy scope | Apply **real-time insights** (recommended thresholds/indicators) |
| Lower **trigger** thresholds | Narrow scope: fewer users, only most sensitive content/channels |
| Lower **indicator** thresholds | Enable **inline alert customization** (tune thresholds during triage) |
| Move **Settings > Intelligent detections** alert volume slider toward "More alerts" | **Bulk-dismiss** low-priority alerts |

- Templates such as **Data leaks** and **Risky browser usage** support custom thresholds that can be lowered.
- Other tuning levers: right policy template, **global exclusions**, **detection groups** (different policies per user population), **indicator variants**, policy **timeframes**, and role assignment for config changes.
- ⚠️ Exam tip: "global exclusions" = stop benign activity scoring everywhere; "detection groups" = apply different policies to different populations.

## Investigate and triage insider risk alerts in Microsoft Purview

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-insider-risk-investigate-alerts/investigate-triage-alerts

- **Alerts dashboard** columns: ID, Copilot icon, Users, Policy, Status (new/confirmed/dismissed/resolved), **Spotlight**, Alert severity (Low/Medium/High, auto-calculated), Time detected, Assigned to, Case.
- Filter by risk factor, severity, policy, analyst, triggering event; save filter views; customize columns; search by UPN, alert ID, or assigned admin.
- **Spotlight** = rule-based highlighting of high-priority alerts (activity type, tags, cross-org scoring patterns); most useful at high volume.
- **Alert details** page: summary (severity, score, triggering activity/event), user details and past alerts, tabs for deeper analysis.
- New insights about the same user are **added to the existing alert**, not raised as a new one.
- Visibility depends on role assignments and **administrative unit** scoping (unrestricted admins see all).
- Triage actions: **Dismiss**, **Confirm & create case**, **Assign**.
- Untriaged alerts can **rise in severity** if more risky activity occurs.
- **Summarize with Copilot** (embedded Security Copilot) gives policy, trigger, activity, key user attributes, and top risk factors without opening the alert.

| Limit | Value |
|---|---|
| Bulk dismiss | Up to **400** alerts at once |
| "Needs review" alert retention | **120 days**, then auto-deleted unless linked to an active case |
| Active/unresolved case retention | Indefinite |
| Active cases per org | Up to **100** |

- ⚠️ Exam tip: memorize **400 / 120 days / 100 cases**.

## Investigate insider risk alerts with Security Copilot and AI agents

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-insider-risk-investigate-alerts/investigate-alerts-security-copilot-agents

- **Insider risk triage agent** (Security Copilot) reviews IRM alerts and ranks them by risk; analysts can steer it with plain-language custom instructions, which it turns into conditions reused on every run.
- Usernames are **pseudonymized by default**; change it in IRM settings if procedures require identities.
- Results appear in the **Alert triage agent (preview)** view on the Alerts page:

| Category | Meaning |
|---|---|
| Needs attention | Highest insider risk; review first |
| Less urgent | Lower severity or likelihood of violation |
| Not categorized | Couldn't be evaluated (e.g. unsupported policy type) |

- Prioritization inputs: **risk indicators**, **policy type** (data theft, security violation, risky AI usage), **user context** (Adaptive Protection risk level, HR signals, role changes).

| Requirement | Detail |
|---|---|
| Roles | Insider Risk Administrator, Insider Risk Analyst, or Security Copilot Contributor |
| Licensing | IRM + provisioned **Security Compute Units (SCUs)** |
| Configuration | Tenant onboarded to Security Copilot with the **Purview plugin** enabled |
| Identity | Runs as the user who last saved the config; renew every **90 days** |

- Runs on a **schedule** or **manually** (per alert or policy); can be rerun when new context appears.
- Only alerts from **active** policies are triaged; **simulation-mode** policies are excluded.
- From an alert: **Agent summary** pane, **Summarize**, then assign, escalate to a case, or apply Adaptive Protection; **Activity explorer** and **Data risk graph** add depth.
- ⚠️ Exam tip: triage agent needs **SCUs + Purview plugin**; simulation-mode alerts are never triaged.

## Analyze alert context with the All risk factors tab

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-insider-risk-investigate-alerts/all-risk-factors-tab

- Summarizes all risk signals around the alert, **including ones that didn't trigger it**.
- Signals shown: top exfiltration activities, cumulative exfiltration, sequences, priority content, unallowed domains, unusual behavior / high-impact user.
- **Content detected** section: item metadata (name, type, location, sensitivity label) and a jump into Activity explorer.
- Sequences can include file types **excluded** from scoring if they contributed to the risky pattern (e.g. an image used for obfuscation).
- ⚠️ Exam tip: always confirm the actual trigger in the **alert summary**; this tab shows context, not cause.

## Investigate activity details with the Activity explorer tab

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-insider-risk-investigate-alerts/activity-explorer-tab

- Event-level list: date/time, activity type, file name/location, sensitivity label, risk score and factors; details pane shows metadata (path, recipient) and contributing indicators.
- Filters: **activity scope** (this alert vs all scored user activity), **risk factor**, **review status** (hide reviewed items).
- Customize columns, sort by date or score, **Save this view** (filters + columns; personal or shared).
- Why counts differ from raw logs: cumulative exfiltration **deduplicates** similar events, policy changes can exclude earlier events, excluded items can still appear in sequences.
- Excluded events show score **0** and are marked **Excluded**, with a link to list them.

## Review patterns over time with the User activity tab

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-insider-risk-investigate-alerts/user-activity-tab

- Color-coded **scatter plot**: each bubble = a scored risk event; vertical axis = risk score, horizontal = time.
- Bubble details: date, risk category (e.g. Exfiltration, Obfuscation), score, linked files/emails.
- **Sequences** = bubbles joined by lines; details show name, date range, combined score, event count.
- Filters: risk category, activity type (e.g. AI usage, deletion), date range **1 / 3 / 6 months**, sort by score or date, review status.
- Spans **multiple alerts**, and shows cumulative exfiltration as a trend line.

| Tab | Best for |
|---|---|
| All risk factors | Which risk signals surround the alert |
| Activity explorer | Event-level detail and metadata |
| User activity | Trends and sequences over time, across alerts |

- ⚠️ Exam tip: "is the behavior escalating over months?" → **User activity**; "what exactly happened in this event?" → **Activity explorer**.

## Investigate insider risk alerts in Microsoft Defender XDR

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-insider-risk-investigate-alerts/investigate-alerts-defender

- Defender portal: **Investigation & response > Incidents & alerts > Incidents**, filter **Service source = Microsoft Purview Insider Risk Management**.
- IRM alerts can be part of multi-source incidents (with MDE, Entra ID, Purview DLP signals), appear alone in the alert queue, or link to the user entity page.

| Defender status | IRM status |
|---|---|
| New, In progress | Needs review |
| Resolved | Dismissed or Confirmed (per classification) |

| Defender classification | IRM classification |
|---|---|
| True positive | Confirmed |
| Informational, expected activity | Dismissed |
| False positive | Dismissed |

- Sync between portals takes about **30 minutes**.
- Advanced hunting tables: `AlertInfo`, `AlertEvidence`, `DataSecurityBehaviors` (policy-triggering behavior patterns), `DataSecurityEvents` (detailed violation events).

```kusto
DataSecurityEvents
| where ActionType == "FileUploaded"
| where FileName endswith ".zip"
```

- Prerequisite: enable **Share user risk details with other security solutions** (Purview **Settings > Insider Risk Management > Data sharing**).
- Roles: Defender **Security Operator** or **Security Reader** plus a Purview IRM role (IRM, **Analyst**, or **Investigator**); hunting data needs IRM **Analyst** or **Investigator**. Both products must be licensed.
- **Not** available in Defender: custom-detection alerts, risky AI usage events, non-Microsoft app events, email exfiltration, events before alert creation, policy-excluded events.
- ⚠️ Exam tip: no IRM alerts in Defender? Check the **Data sharing** setting first.

## Manage and take action on insider risk cases

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-insider-risk-investigate-alerts/insider-risk-cases

- A case = **one user**, one or more alerts; created **manually** from alerts (confirm → create case). Recommended for serious, repeated, or cross-team issues.
- Assignable to users with an IRM, Analyst, or Investigator role.
- **Cases dashboard**: name/ID, user (anonymized if enabled), status **Active/Closed**, alert count, opened/updated times, last updated by; filters, custom columns, saved views.

| Case tab | Contents |
|---|---|
| Case overview | Identity, department, risk score, alerts |
| Alerts | Status, severity, ID per alert |
| User activity | Risk timeline (alert or broader history) |
| Activity explorer (preview) | Event-level detail within case scope |
| Forensic evidence | Screen captures of triggering activity |
| Content explorer | Copies of related files and emails |
| Case notes | Permanent, timestamped analyst notes |
| Contributors | Collaborators (view + add notes only) |

| Case action | Detail |
|---|---|
| Send email notice | Template-based; logged in Case notes; does **not** close the case |
| Escalate for investigation | Creates an **eDiscovery (Premium)** case (legal hold workflows) |
| Run Power Automate flows | e.g. notify manager, ServiceNow record, HR request |
| Teams team | Auto-created per case if enabled (Settings > IRM > Microsoft Teams); archived on resolve |
| Resolve case | **Benign** or **Confirmed policy violation**, with a reason; status → Closed |

- ⚠️ Exam tip: contributors **can't** confirm/dismiss alerts or edit the contributor list.

## Exercise - Investigate potential data theft using Insider Risk Management

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-insider-risk-investigate-alerts/exercise-investigate-alert

- Click-through simulation of a departing-employee data theft alert: review All risk factors, Activity explorer, User activity, and Copilot summaries.
- Confirms alerts into a case, escalates to eDiscovery, and resolves as a confirmed policy violation.
- Switches to Defender to see the IRM alert inside an incident (attack story, alerts, assets, evidence tabs).

## Key terms

| Term | Meaning |
|---|---|
| IRM | Microsoft Purview Insider Risk Management |
| Triggering event | Event that starts active risk scoring of a user under a policy |
| Spotlight | Rule-based highlighting of high-priority IRM alerts |
| Inline alert customization | Lets analysts adjust policy thresholds while triaging an alert |
| Global exclusions | Settings that stop benign activity from scoring across policies |
| Detection groups | Apply different policies to different user populations |
| Cumulative exfiltration | Detection of repeated exfiltration building up over time; deduplicates similar events |
| Sequence | Related activities that together form a risk pattern |
| Insider risk triage agent | Security Copilot agent that ranks IRM alerts (Needs attention / Less urgent / Not categorized) |
| SCU | Security Compute Unit: provisioned capacity for Security Copilot |
| Pseudonymization | Default masking of usernames in IRM |
| `DataSecurityEvents` | Advanced hunting table of detailed IRM policy-violation events |
| `DataSecurityBehaviors` | Advanced hunting table of IRM policy-triggering behavior patterns |

## Exam traps

- All risk factors vs trigger: the tab shows **all** surrounding signals; the alert summary shows what actually **triggered** it.
- Activity explorer vs User activity: event-level detail vs timeline/trend across alerts.
- Send notice vs Resolve: a notice never closes a case; **Resolve case** does.
- Escalate for investigation → **eDiscovery (Premium)**, not a Defender incident.
- Defender Resolved ≠ one IRM status: it maps to Dismissed **or** Confirmed based on classification; False positive and Informational both → Dismissed.
- Missing IRM data in Defender: email exfiltration, risky AI usage, custom detections, non-Microsoft apps are never shared.
- Triage agent skips alerts from **simulation-mode** policies.

## Top 3 takeaways

1. Alert = policy + triggering event + risk score over threshold; tune volume with indicators, thresholds, scope, and the Intelligent detections slider.
2. Triage in the Alerts dashboard (Spotlight, Copilot, triage agent), investigate with All risk factors / Activity explorer / User activity, escalate serious findings to a one-user case.
3. With Data sharing enabled, IRM alerts appear in Defender XDR incidents and `DataSecurityEvents`/`DataSecurityBehaviors` hunting tables, with status synced in ~30 min.
