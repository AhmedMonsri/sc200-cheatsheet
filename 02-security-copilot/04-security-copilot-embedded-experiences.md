# Module: Describe the embedded experiences of Microsoft Security Copilot

> Learning path: [Mitigate threats using Microsoft Security Copilot](https://learn.microsoft.com/en-us/training/paths/sc-200-mitigate-threats-using-microsoft-copilot-for-security/) | Source: [Microsoft Learn module](https://learn.microsoft.com/en-us/training/modules/security-copilot-embedded-experiences/)
> Original time: ~49 min (7 units, excl. assessment) | Read time: ~5 min | Last verified: 2026-09-27

## TL;DR

- The **embedded experience** = Security Copilot surfaced inside a specific product's portal (Defender XDR, Purview, Entra, Intune, Defender for Cloud, and more).
- The host product determines which scenarios are available; Copilot calls that product's capabilities directly, which is more efficient than the standalone experience working out which capability to use.
- Embedded is a good starting point (e.g. summarize an incident from the incident page); pivot to the **standalone** experience ("Open in Security Copilot") for deeper, cross-product investigation.
- Across products, Copilot works with the **signed-in user's permissions** and needs the product's **plugin enabled** in Security Copilot.

## Exam objectives covered

| Exam domain | Objective (paraphrased) |
|---|---|
| Respond to security incidents | Investigate incidents with agentic AI, incl. embedded Security Copilot |
| Respond to security incidents | Investigate/remediate threats and compromised entities identified by Purview (Copilot DLP / insider risk alert summaries) |
| Respond to security incidents | Investigate/remediate compromised identities identified by Entra ID (Copilot risky user summaries) |
| Perform threat hunting | Create Advanced Hunting queries (Copilot query assistant: natural language → KQL) |
| Perform threat hunting | Interpret threat analytics in Defender XDR (Copilot summaries of threat intel) |

## Embedded vs standalone at a glance

| | Embedded | Standalone |
|---|---|---|
| Where | Inside the product portal | Security Copilot portal |
| Scope | Scenarios of the host product | All capabilities/plugins enabled for your role |
| Processing | Invokes product-specific capabilities directly | Selects the best-suited capability for the prompt |
| Best for | Starting an investigation in context | Detailed, cross-product investigation |

## Describe Copilot in Microsoft Defender XDR

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-embedded-experiences/2-copilot-for-defender

- Prerequisite: the **Microsoft Defender XDR plugin** must be enabled, done from the **standalone** experience.
- The unit includes a short video demoing a sample of the embedded Defender capabilities.

| Capability | What it does | Where / key facts |
|---|---|---|
| Incident summary | Auto-generated overview: what happened, assets involved, attack timeline, IOCs, threat actor names; suggests follow-up prompts on identities, devices, IPs | Appears when opening an incident page; up to **100 alerts** per summary |
| Guided responses | AI-recommended actions, each card explaining why | Categories: **Triage, Containment, Investigation, Remediation**; admins can upload org-specific response guidelines; for incident types such as phishing, BEC, ransomware |
| Script / command-line analysis | Plain-language explanation of a (possibly obfuscated) script, malicious or not, MITRE ATT&CK techniques used; "Show code" highlights relevant lines | From the alert timeline in an incident; supports **PowerShell, batch, bash** |
| Query assistant | Converts natural-language questions to ready-to-run KQL | Advanced hunting; query can be run, added to the editor, or copied |
| Incident report | Consolidated record from Sentinel + Defender XDR data: management action timestamps, analysts, classification and comments, manual/automated actions (incl. Sentinel playbooks), follow-ups | Generated in the portal |
| File analysis | Summary with detections, file certificates, API calls, strings | File page; reached via evidence and response tab, incident graph, or search |
| Device summary | Protection status (e.g. ASR, tamper protection), unusual user activity, vulnerable software, firewall settings, Intune info | Device page |
| Identity summary | Account creation date, criticality, roles and changes, sign-in patterns, auth methods, Entra ID risks, contact info | Identity page |
| Threat intelligence | Summarizes threats affecting you, prioritizes by exposure, finds actors targeting your industry | Uses the **Microsoft Defender Threat Intelligence** plugin (threat analytics reports, intel profiles, vulnerability disclosures) |

- Common to all features: feedback on AI output, and **Open in Security Copilot** (via the ellipses) to move to standalone.
- ⚠️ Exam tip: incident **summary** = what the attack was; incident **report** = what the SOC did about it (who, when, classification, actions).

## Copilot in Microsoft Purview

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-embedded-experiences/3-copilot-for-purview

- Access: **Copilot icon** in the top navigation bar, available across all Purview solutions.
- Prerequisites: org onboarded to Security Copilot; **Purview plugin** enabled; licensed/onboarded to the relevant Purview solution; owner plugin setting allowing Security Copilot to **access data from Microsoft 365 services** turned on.
- Roles: for Copilot, at least **Copilot workspace contributor** or Entra **Security operator**; for Purview, Copilot uses the **user's own** solution permissions.

| Solution | Copilot capability | Key limits / facts |
|---|---|---|
| DSPM | Explore unprotected sensitive data and risky user activity; built-in promptbooks **Risky user investigation** and **Sensitive data protection**; prompt gallery by category | Insights come from DLP, Information Protection, and Insider Risk Management data |
| DLP | Summarize an alert from the DLP alerts queue; explain what DLP policies do and where they're active | Summary options: copy, regenerate, Open in Security Copilot |
| Insider Risk Management | Summarize alerts (exfiltration, patterns, user roles, unusual activity); summarize a user's activities over a time period | "Summarize" from the alerts queue |
| Activity explorer | Natural-language drill-down into activities, sensitive files, users; natural language → **filter set** you review and apply | — |
| Communication Compliance | Contextual summary of a message + attachments against the classifier conditions that flagged it; follow-up questions | Trainable classifiers only; combined length **≥ 100 words**; needs **Communication Compliance** or **Communication Compliance Investigator** role |
| eDiscovery | Contextual summary of a **single review set item**; natural-language search; case summaries | Only file types with text extraction support |

- Feedback options: confirmed / off target, inaccurate / potentially harmful, inappropriate.
- ⚠️ Exam tip: Copilot never grants extra access in Purview; it inherits the user's Purview permissions.

## Copilot in Microsoft Entra

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-embedded-experiences/4-copilot-for-entra

- Prerequisites: onboarded to Security Copilot, **Entra plugin** enabled, user holds the roles needed for the Entra data (Copilot uses the user's permissions).
- Access: global Copilot button in the **Entra admin center** menu bar; starter prompts, free-text prompts, and suggested follow-up prompts.

| Product area | Copilot scenarios |
|---|---|
| Entra ID | User/group/domain/license lookups; sign-in, audit, provisioning log analysis; Conditional Access evaluation and gap finding; authentication methods and registration; role assignments; recommendations and health alerts; device details and compliance |
| Entra ID Protection | **Risky user** summary (risk level, context, mitigation recommendations, risk history); **application risk** for workload identities (high-privilege permissions, unused apps, external apps, app risk level) |
| Entra ID Governance | Access reviews (approvers, reviewers with no decision, AI recommendation overrides); entitlement management (access packages, policies, connected orgs, catalogs); **PIM** insights plus write action to activate the least-privileged role in-chat; lifecycle workflows (JML) |
| Internet Access / Private Access | Global Secure Access traffic analysis: usage, bandwidth, blocked traffic, app access patterns, cross-tenant access |

- App risk starter prompts appear in **Enterprise apps**, **App registrations**, and **Identity Protection > Risky workload identities**.
- **Data exploration**: responses with **more than 10 items** offer **Open list** → full data grid showing the underlying **Microsoft Graph URL** for verification.
- Feedback: thumbs up / thumbs down.

## Copilot in Microsoft Intune

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-embedded-experiences/5-copilot-for-intune

- Prerequisites: Security Copilot configured, **Intune plugin** enabled, appropriate roles; **no extra license** beyond Security Copilot.
- Access control: Entra **Intune Administrator** has access by default; other roles are granted through Security Copilot; **no built-in Intune role** includes Copilot access.
- Scope: Copilot sees only what the admin can see (**RBAC roles + scope tags**).
- Status check: Intune admin center > **Tenant administration > Copilot**.
- Windows 365 Cloud PC scenarios also need the **Windows 365 plugin**.
- Access: **Copilot** button in the top banner → Copilot Chat; prompt sections **Suggestions**, **Explore your data** (may link to data explorer via "Explore further"), **Check documentation**.

| Scenario | What Copilot does |
|---|---|
| Policy and setting management | Setting tooltips (impact, recommended value, used in other policies); summarize an existing policy (purpose, assignments, settings); find conflicting settings in **compliance** policies |
| Device details and troubleshooting | Summarize or compare devices, show apps/policies/groups/primary user; **error analyzer** for error codes |
| Data exploration | Natural-language queries over devices, users, apps, policies, updates, compliance; results can add users/devices to groups or build custom reports |
| Device query | Generates **KQL** for Intune device query (single or many devices) with an explanation; requires **Intune Advanced Analytics** licensing |
| Endpoint Privilege Management | Context to approve or deny elevation requests |
| Surface devices | Troubleshooting via the Surface Management Portal in the Intune admin center |
| Windows 365 Cloud PCs | Resize recommendations, connectivity issue summaries, unused license detection, provisioning / grace period diagnosis |

- Supported policy types: compliance, device configuration (incl. settings catalog), most endpoint security policies.
- ⚠️ Exam tip: Copilot in Intune is licensed with Security Copilot, but **device query** still needs Advanced Analytics.

## Copilot in Microsoft Defender for Cloud

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-embedded-experiences/5a-copilot-for-defender-cloud

- **Dual-platform architecture**: Copilot for Azure receives the prompt and evaluates it with the active page → if a matching Security Copilot skill exists, **Security Copilot** runs it; otherwise Copilot for Azure answers with its own skills.
- **SCU consumption**: only prompts that invoke Security Copilot skills consume SCUs; Copilot for Azure-only prompts don't.
- Prerequisites: Defender for Cloud enabled; access to Azure Copilot (on by default unless a Global Administrator restricts it); **SCUs** provisioned.
- No Defender for Cloud plan required, but the **Defender CSPM (DCSPM)** plan is recommended for full capability (attack path analysis, risk prioritization); without it, capability is limited.

| Action | What happens |
|---|---|
| Analyze | **Analyze with Copilot** on the recommendations page; prompts (e.g. publicly exposed, sensitive data, critical resources) filter the recommendations list |
| Summarize | Plain-language overview of a recommendation's risk, why it matters, impact |
| Remediate | Step-by-step guidance; some recommendations include a runnable script |
| Delegate | Copilot drafts an email to the resource owner; track progress on the recommendations page |
| Remediate code | For **IaC** scanning findings: **Reduce risk with Copilot** generates a fix and opens a **pull request**; a developer must review/approve |

- Code remediation extra prerequisites: Azure DevOps connected to Defender for Cloud, **Microsoft Security DevOps** Azure DevOps extension, DevOps security prerequisites met.
- Feedback: Looks right / Needs improvement / Inappropriate.
- ⚠️ Exam tip: this unit covers **posture recommendations**, not workload protection alerts.

## Key terms

| Term | Meaning |
|---|---|
| Embedded experience | Security Copilot used inside a product portal, limited to that product's scenarios |
| Standalone experience | The Security Copilot portal, for cross-product investigation with all enabled capabilities |
| Plugin | Connector that lets Security Copilot use a product's data/skills; must be enabled for each embedded experience |
| Guided responses | Defender XDR Copilot recommendations in Triage, Containment, Investigation, Remediation categories |
| Query assistant | Defender advanced hunting feature that turns natural language into KQL |
| Incident report | Copilot-generated record of incident handling (timeline of management actions, analysts, classification, actions) |
| Promptbook | Sequence of prompts that run automatically in order (e.g. DSPM Risky user investigation) |
| DSPM | Data Security Posture Management (Purview) |
| SCU | Security Compute Unit: capacity unit consumed by Security Copilot |
| DCSPM | Defender Cloud Security Posture Management plan in Defender for Cloud |
| Copilot for Azure | Azure's Copilot; front end of the dual-platform architecture in Defender for Cloud |
| IaC | Infrastructure as Code |
| EPM | Endpoint Privilege Management (Intune elevation requests) |
| JML | Joiner, mover, leaver lifecycle scenarios (Entra lifecycle workflows) |

## Exam traps

- Embedded vs standalone: embedded = in-context, product-scoped, more efficient; standalone = cross-product. Pivot with **Open in Security Copilot**.
- Plugin vs permission: the plugin must be enabled **and** the user needs the product's roles; Copilot never exceeds the user's access (Intune: RBAC + scope tags).
- Incident summary vs incident report: summary = attack overview (≤ 100 alerts); report = response/management record incl. Sentinel playbook actions.
- Intune access: Entra **Intune Administrator** by default; no built-in Intune role grants Copilot; no Intune-specific license, except **Advanced Analytics** for device query.
- Defender for Cloud SCUs: only Security Copilot skill executions consume SCUs; Copilot for Azure responses don't. DCSPM is recommended, not required.
- Communication Compliance summaries: trainable classifiers only, ≥ 100 words, specific roles.
- eDiscovery summaries: one review set item at a time, text-extractable file types only.
- Script analysis languages: PowerShell, batch, bash.

## Top 3 takeaways

1. Embedded Copilot works in context inside each product, uses that product's plugin and the user's permissions, and hands off to standalone for cross-product work.
2. Defender XDR is the core SOC surface: incident summaries, guided responses, script/file analysis, NL-to-KQL, incident reports, device/identity summaries, threat intel.
3. Each product has its own prerequisites and limits worth memorizing: Purview (M365 data access setting, roles), Entra (>10 items → Open list), Intune (Intune Administrator, Advanced Analytics), Defender for Cloud (SCUs, DCSPM, IaC PRs).
