# Module: Remediate threats using Microsoft Defender

> Learning path: [Mitigate threats using Microsoft Defender XDR](https://learn.microsoft.com/en-us/training/paths/sc-200-mitigate-threats-using-microsoft-365-defender/) | Source: [Microsoft Learn module](https://learn.microsoft.com/en-us/training/modules/m365-threat-remediate/)
> Original time: ~59 min (6 units, excl. assessment) | Read time: ~6 min | Last verified: 2026-09-27

## TL;DR

- Microsoft Defender for Office 365 (MDO) is the cloud email-filtering component of Defender XDR: zero-day malware protection, time-of-click link protection, anti-phishing, reporting, and URL trace.
- Protection is **policy-driven** (Safe Attachments, Safe Links, anti-phishing), configured in the Microsoft Defender portal and scoped to users, groups, recipients, or domains.
- **AIR** playbooks investigate email threats automatically and queue remediation actions for SOC approval.
- The **Phishing Triage Agent** (Security Copilot) classifies user-reported phishing alerts as true or false positives and explains each verdict.
- Threat trackers, Threat Explorer, and attack simulation support threat analysis and user-risk reduction.

## Exam objectives covered

| Exam domain | Objective (paraphrased) |
|---|---|
| Respond to security incidents | Investigate and remediate threats detected by Defender for Office 365 |
| Respond to security incidents | Investigate with agentic AI, incl. Security Copilot agents in Defender (Phishing Triage Agent) |
| Manage a security operations environment | Automated investigation and response (AIR) in Defender XDR |
| Manage a security operations environment | Alert tuning (partial: impact of the "Auto-Resolve" tuning rule on the agent) |

## MDO deployment options (from the introduction)

| Scenario | What MDO protects |
|---|---|
| Filtering-only | Cloud email protection in front of on-premises Exchange or another on-premises SMTP system |
| Cloud | Exchange Online mailboxes |
| Hybrid | Mixed on-premises and cloud mailboxes, with EOP handling inbound filtering and MDO controlling routing |

## Automate, investigate, and remediate

> Unit: https://learn.microsoft.com/en-us/training/modules/m365-threat-remediate/automate-investigate-remediate

- **AIR** = a set of security playbooks, launched **automatically** (e.g. when an alert fires) or **manually** (e.g. from a Threat Explorer view).
- Typical case: a URL is clean at delivery and weaponized later → Safe Links URL detonation detects it → alert → AIR playbook runs.
- The alert's investigation link opens the **investigation graph**: entities (emails, URLs, users and their activity, devices) and their relationships.
- Strong compromise indicator in the example: malicious mail sent by an **internal** user, plus a suspicious sign-in and mass document downloads for that user.
- Example auto-remediations: block the URL, delete related emails, trigger Microsoft Entra password reset and MFA workflows for the compromised user.

| Remediation action (needs SecOps approval) | Effect |
|---|---|
| Soft delete email messages or clusters | Removes the messages from mailboxes |
| Block URL (time-of-click) | Blocks the link when users click it |
| Turn off external mail forwarding | Stops mail being forwarded outside the org |
| Turn off delegation | Removes delegated mailbox access |

- Actions awaiting approval appear in the investigation's **Pending actions** tab.
- ⚠️ Exam tip: AIR can act automatically **or** wait for manual approval, depending on policy. MDO remediation actions usually require approval.

## Configure, protect, and detect

> Unit: https://learn.microsoft.com/en-us/training/modules/m365-threat-remediate/configure-protect-detect

- Policies are defined in the Defender portal and can be scoped at user, organization, recipient, and domain level; review them regularly.

### Safe Attachments

- Attachments with no known malware signature are detonated in a dedicated analysis environment (ML + analysis); clean messages are then delivered.

| Action for unknown malware | Behavior |
|---|---|
| Off | No scanning |
| Monitor | Delivers the message anyway and tracks scan results |
| Block | Blocks current and future emails/attachments with the detected malware |
| Replace | Removes the malicious attachment, delivers the message body |
| Dynamic delivery | Delivers the body immediately, reattaches the attachment once scanned as safe |

- **Redirect attachment on detection**: send blocked/replaced/monitored attachments to an admin mailbox; optionally apply the same action if scanning times out or errors.
- Targeting by domain, user, or group (combinable), with exceptions by user, group, or domain.
- Bypass for trusted internal senders (scanners, faxes): Exchange admin center **mail flow (transport) rule** setting header `X-MS-Exchange-Organization-SkipSafeAttachmentProcessing`.
- Don't bypass all internal mail: a compromised internal account could send malware.
- ⚠️ Exam tip: **Replace** = body delivered, attachment stripped; **Dynamic delivery** = body now, attachment later if safe; **Monitor** = delivered despite detection.

### Safe Links

- Checks URLs at **every click** in email and Office documents; malicious links blocked, good links allowed.

| Supported location | Examples |
|---|---|
| Desktop apps | Microsoft 365 Apps for enterprise (Windows, Mac); Word, Excel, PowerPoint, Visio on Windows |
| Web | Word, Excel, PowerPoint, OneNote for the web |
| Mobile | Office apps on iOS and Android |
| Collaboration | Microsoft Teams channels and chats |

- Client- and location-agnostic: device and location don't change wrapped-link behavior; Office 2016 supported when signed in with Office 365 credentials.
- **Default policy** holds global settings (which links to block/wrap): **can't be deleted**, can be edited. Microsoft recommends applying Safe Links to **all users**.

| Policy setting | Effect |
|---|---|
| Action for unknown potentially malicious URLs = On | URLs rewritten and checked |
| Use Safe Attachments to scan downloadable content | Files behind links are detonated; malicious → warning page |
| Apply to messages sent within the organization | Same protection for internal email |
| Do not track when users click | Disables click tracking (Microsoft recommends leaving tracking **on**) |
| Do not allow users to click through to the original URL | Blocks proceeding to a malicious site |
| Do not rewrite the following URLs | Allow list for known-safe sites (e.g. partner sites) |

- Bypass via mail flow rule header `X-MS-Exchange-Organization-SkipSafeLinksProcessing`.

### Anti-phishing policies

- For users covered by MDO policies, multiple ML models evaluate incoming messages; the configured policy decides the action.
- **Impersonation** = sender or domain that looks like a real one (look-alike characters, a missing letter); the domain may be registered and authenticated but intends to deceive.
- MDO-exclusive settings: users to protect, domains to protect, actions for protected users (e.g. redirect, Junk folder), safety tips, trusted senders and domains. Anti-spoofing settings are also included.
- ⚠️ Unverified: the unit says there is no default MDO anti-phishing policy. Current [docs](https://learn.microsoft.com/en-us/defender-office-365/anti-phishing-policies-about) state a default anti-phishing policy applies to all recipients, with custom policies for specific users, groups, or domains. Prefer the docs.
- The unit ends with an interactive guide (also available as video) walking through MDO policy configuration in the Defender portal; no additional testable content.

## Microsoft Security Copilot Phishing Triage Agent in Microsoft Defender

> Unit: https://learn.microsoft.com/en-us/training/modules/m365-threat-remediate/phishing-triage-agent

- AI agent that triages **user-reported** phishing: LLM-based analysis classifies each submission as a real threat or a false positive, using contextual reasoning rather than static rules.
- Capabilities: autonomous triage, natural-language rationale plus visual reasoning map, learning from analyst feedback.
- Tools used: email content analysis, file/URL detonation, screenshot analysis, Microsoft Threat Intelligence, advanced hunting across data sources.

| Prerequisite | Requirement |
|---|---|
| Security Copilot | Provisioned capacity in Security Compute Units (SCU) |
| Licensing | **Defender for Office 365 Plan 2** deployed |
| URBAC | Unified RBAC enabled for the MDO workload (Defender XDR settings) |
| User reported settings | "Monitor reported messages in Outlook" on, with a reported-message destination |
| Alert policy | "Email reported by user as malware or phish" turned **on** |
| Alert tuning | Built-in "Auto-Resolve – Email reported by user as malware or phish" rule **disabled** (suppressed alerts aren't classified) |
| Plugins (auto-activated) | Defender XDR, Microsoft Threat Intelligence, Phishing Triage Agent |

- Required permissions (Security operations group, scoped to the **MDO data source**): Security data basics (read), Alerts (manage), Security Copilot (read), Email & collaboration metadata (read), Email & collaboration content (read).
- Setup wizard: from the **Security Store** or the **Incidents queue → Set up agent**.

| Identity option | Notes |
|---|---|
| New Microsoft Entra **Agent ID** (recommended) | Purpose-built for AI agents; scoped, easier to manage; role dropdown shows only suitable roles |
| Existing user account | Inherits the account's access; assign permissions before setup; use a long expiry and monitor auth; **not compatible with PIM or TAP** |

- Least privilege: grant only the required permissions; the team monitoring the agent needs **equal or higher** permissions.
- Trigger: runs automatically when a user reports a suspicious email and an alert is created.

| Verdict | What the agent does |
|---|---|
| False Positive | Classifies and **resolves** the alert |
| True Positive | Classifies as malicious; incident stays **open / in progress** for an analyst |

- During triage the agent assigns the alert to itself and adds an **"Agent" tag** to the incident (filterable in the queue).
- Review: incident → Phishing Triage Agent card (Copilot or Tasks side panel, Guided Response Triage) → **View agent activity**.
- Feedback: Change classification → give reason → "Use this feedback to teach the agent" → Evaluate feedback → Save as a lesson.
- Feedback limits: **once per alert**, phishing classification only; keep it specific, decisive, and consistent with earlier feedback.
- Management: **Security Copilot > Agents** → Agents in use → Go to agent.
  - Overview tab: status, identity, role, recent activity.
  - Performance tab: daily activity, **mean time to triage (MTTT)**, SCU consumption.
  - Actions: pause/run, change identity and role, manage feedback, remove.
- Removing the agent stops new triage, **deletes all feedback**, **keeps history**.
- ⚠️ Exam tip: the agent handles **user-reported** phishing alerts only, needs MDO P2 + SCU capacity, and won't see alerts auto-resolved by tuning.

## Simulate attacks

> Unit: https://learn.microsoft.com/en-us/training/modules/m365-threat-remediate/simulate-attacks

- These tools now live in the Defender XDR portal.

| Tool | Purpose |
|---|---|
| Threat trackers | Latest intel on prevailing threats; types: Noteworthy, Trending, Tracked queries, Saved queries |
| Threat Explorer (real-time detections) | Real-time report to identify and analyze recent email threats, custom time ranges |
| Attack simulation | Run realistic attack scenarios to find vulnerable users |

- Explorer shows threat families over time, top threats, and top targeted users; filters include sender, recipients, and **detection technology**.
- Detection technology shows whether mail was stopped by sandboxing or an EOP filter.
- ⚠️ Unverified: the unit attributes sandboxing to "Microsoft Defender for Cloud"; in context this most likely means MDO detonation.
- Threat detail view: definition, message traces, technical and global details, advanced analysis.
- Top targeted users tab: recipient, subject, sender domain, sender IP, **Delivery action** (blocked vs delivered as spam); if opened, status shows it so you can follow up (e.g. scan the device).
- The docs name the feature **Attack simulation training**: requires **MDO Plan 2 or Microsoft 365 E5**, located under Email & collaboration > Attack simulation training ([docs](https://learn.microsoft.com/en-us/defender-office-365/attack-simulation-training-get-started)).
- ⚠️ Unverified: the unit lists spear phishing, credential harvest, attachment, password spray, and brute-force simulations; check the docs for the current technique list.

## Key terms

| Term | Meaning |
|---|---|
| MDO | Microsoft Defender for Office 365: cloud email and collaboration protection |
| EOP | Exchange Online Protection: baseline email filtering |
| AIR | Automated investigation and response: playbooks that investigate and propose/perform remediation |
| Safe Attachments | Detonates unknown attachments before delivery (zero-day malware protection) |
| Safe Links | Time-of-click URL checking in email, Office docs, and Teams |
| Dynamic delivery | Safe Attachments action: body delivered now, attachment after scanning |
| Impersonation | Look-alike sender or domain used to deceive recipients |
| Phishing Triage Agent | Security Copilot agent classifying user-reported phishing alerts |
| SCU | Security Compute Unit: provisioned Security Copilot capacity |
| Agent ID | Microsoft Entra identity type created for AI agents |
| URBAC | Unified role-based access control in Defender XDR |
| MTTT | Mean time to triage (agent performance metric) |
| Threat Explorer | Real-time email threat analysis report (a.k.a. real-time detections) |

## Exam traps

- **Replace vs Dynamic delivery**: Replace strips the attachment permanently; Dynamic delivery reattaches it if safe.
- **Monitor vs Off**: Monitor scans and tracks but still delivers; Off doesn't scan.
- **Bypass headers**: `SkipSafeAttachmentProcessing` vs `SkipSafeLinksProcessing`, both set via an EAC mail flow rule, not a policy exception.
- **Safe Links default policy**: exists, editable, not deletable.
- **Agent verdicts**: FP → resolved by the agent; TP → left open for an analyst.
- **Agent identity**: Agent ID recommended; a user account works but isn't PIM/TAP compatible.
- **Agent + alert tuning**: the Auto-Resolve tuning rule must be off, or the agent never sees those alerts.
- **Removing the agent**: feedback deleted, history kept.
- **Threat trackers vs Threat Explorer**: trackers = intel on prevailing threats; Explorer = real-time data about threats in your tenant.

## Top 3 takeaways

1. MDO protection comes from three policy types: Safe Attachments (detonation), Safe Links (time-of-click), anti-phishing (impersonation and spoofing).
2. AIR investigates email threats end to end and queues remediation (soft delete, block URL, disable forwarding/delegation) for SOC approval.
3. The Phishing Triage Agent auto-resolves false-positive user reports and escalates true positives; it needs MDO P2, SCU capacity, URBAC, and least-privilege permissions.
