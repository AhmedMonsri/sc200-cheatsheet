# Module: Secure your cloud apps and services with Microsoft Defender for Cloud Apps

> Learning path: [Mitigate threats using Microsoft Defender XDR](https://learn.microsoft.com/en-us/training/paths/sc-200-mitigate-threats-using-microsoft-365-defender/) | Source: [Microsoft Learn module](https://learn.microsoft.com/en-us/training/modules/microsoft-cloud-app-security/)
> Original time: ~64 min (8 units, excl. assessment) | Read time: ~5 min | Last verified: 2026-09-27

## TL;DR

- Defender for Cloud Apps (MDCA) is Microsoft's **CASB**: it sits between users and cloud services to give visibility, data control, and threat detection across Microsoft and third-party apps.
- **Cloud Discovery** finds Shadow IT from traffic logs (after the fact); **Conditional Access App Control** controls access and sessions **in real time**.
- Sensitive data is protected through a 4-phase flow (discover → classify → protect → monitor) using sensitivity labels and **file policies**.
- Built-in **anomaly detection policies** (UEBA + ML) flag deviations from a learned baseline and can be tuned (sensitivity, suppression, scope).

## Exam objectives covered

| Exam domain | Objective (paraphrased) |
|---|---|
| Respond to security incidents | Identify and remediate security risks identified by Defender for Cloud Apps |
| Manage a security operations environment | Tune and suppress alerts (partial: MDCA anomaly policy sensitivity, suppression, scoping) |

## Understand the Defender for Cloud Apps Framework

> Unit: https://learn.microsoft.com/en-us/training/modules/microsoft-cloud-app-security/cloud-app-security-framework

- **CASB** (Gartner definition, paraphrased): a policy enforcement point between cloud service consumers and providers that applies enterprise security policies as cloud resources are accessed.
- Analogy: a CASB is to cloud services what a firewall is to the corporate network.
- MDCA covers Microsoft **and third-party** cloud services and integrates natively with other Microsoft security products.

| Framework element | What it does |
|---|---|
| Discover and control Shadow IT | Identify cloud apps, IaaS, and PaaS in use; unknown apps (often 1,000+) = Shadow IT |
| Protect sensitive information | Understand, classify, and protect data at rest; DLP across data leak points |
| Protect against cyberthreats and anomalies | Detect unusual behavior (incl. ransomware) via anomaly detection, UEBA, and rule-based activity detections |
| Assess compliance | Compare apps and usage against regulations/standards; block leaks to noncompliant apps; limit access to regulated data |

- ⚠️ Exam tip: know the four framework elements by name; questions often describe a scenario and ask which element applies.

## Explore your cloud apps with Cloud Discovery

> Unit: https://learn.microsoft.com/en-us/training/modules/microsoft-cloud-app-security/cloud-discovery

- Analyzes **traffic logs** against a catalog of **16,000+ cloud apps**, scoring each on **80+ risk factors**.
- App **risk score: 1–10** (security, compliance, regulatory factors).
- Dashboard review order: high-level usage (top users, source IPs) → app categories and share of **Sanctioned** use → **Discovered apps** tab → **App risk overview** → **App Headquarters map**.
- Risky app → tag it **Unsanctioned** in Discovered apps.

| Environment | How an unsanctioned app gets blocked |
|---|---|
| Defender for Endpoint (or similar) in use | Blocked automatically |
| No threat protection solution | Run a script against the data source; users see a "blocked" notification |

- ⚠️ Exam tip: Cloud Discovery is **retrospective** visibility (log-based), not real-time control.

## Protect your data and apps with Conditional Access App Control

> Unit: https://learn.microsoft.com/en-us/training/modules/microsoft-cloud-app-security/conditional-access-app-control

- Real-time monitoring and control of user **app access and sessions**, including on BYOD/unmanaged devices.
- Works by integrating with the **IdP**; with **Entra ID** it is integrated directly.
- Entra **Conditional Access** conditions (who: users/groups; what: cloud apps; where: locations/networks) route users to MDCA, which applies access and session controls.
- Setup in Entra CA policy: **Access controls → Session → Use Conditional Access App Control** → built-in policies or **custom controls** (custom = defined in MDCA).

| Control | What it does |
|---|---|
| Prevent data exfiltration | Block download, cut, copy, print (e.g. on unmanaged devices) |
| Protect on download | Allow download but apply a label and protection instead of blocking |
| Prevent upload of unlabeled files | Block upload until the file is classified |
| Monitor sessions for compliance | Log risky users' in-app actions for later analysis |
| Block access | Block specific apps/users based on risk factors (e.g. device management signals such as client certificates) |
| Block custom activities | Scan and block app-specific actions in real time (e.g. sensitive Teams/Slack messages) |

- Example (Teams): session policy from template **Block sending of messages based on real-time content inspection** → activity source **Send Teams message** → **Content inspection** (preset, custom, or regex expression) → action **Block** + alert admins → user sees a notification.
- Prerequisite for the example: an Entra CA policy set to **Use custom controls**.
- ⚠️ Exam tip: the CA policy lives in **Entra ID**; the granular session/access policy logic lives in **MDCA**.

## Walk through discovery and access control with Microsoft Defender for Cloud Apps

> Unit: https://learn.microsoft.com/en-us/training/modules/microsoft-cloud-app-security/walkthrough

- Video-only unit (~26 min) demonstrating Cloud Discovery and Conditional Access App Control in the Microsoft Defender portal.
- No new concepts in text; it applies the two previous units hands-on.

## Classify and protect sensitive information

> Unit: https://learn.microsoft.com/en-us/training/modules/microsoft-cloud-app-security/classify-protect-sensitive-information

- MDCA integrates natively with **Azure Information Protection** (AIP) to classify and protect files and email.
- ⚠️ Unverified: AIP labeling capabilities are now delivered through Microsoft Purview Information Protection; the unit still uses the AIP name.
- Requires the **Microsoft 365 app connector** to be enabled.

| Phase | Key actions |
|---|---|
| 1. Discover data | Connect apps via an **app connector** or **Conditional Access App Control** |
| 2. Classify | Use **100+ built-in sensitive information types** and default labels; enable **Automatically scan new files for AIP classification labels** in Settings |
| 3. Protect | Create policies (e.g. **file policy**) with governance actions |
| 4. Monitor and report | Alerts pane, filter Category = **DLP**; investigate, dismiss, or export alerts to CSV |

- Default labels (least → most sensitive): **Personal**, **Public**, **General**, **Confidential**, **Highly confidential**.
- File policy: scans file content in real time and at rest; default **Category = DLP**.
- File policy governance actions: alerts/email notifications, change sharing, quarantine, remove file/folder permissions, move to trash.
- File policy fields: severity, category, file filter (keep narrow), apply to (all files excl. selected folders / selected folders), owners (all / selected groups / all excl. groups), inspection method, governance.

| Content inspection method | Note |
|---|---|
| Built-in DLP | MDCA's own engine |
| Data Classification Services (DCS) | **Recommended**: unified labeling across Microsoft 365, AIP, and MDCA |

- ⚠️ Exam tip: DLP-related MDCA alerts are found by filtering alerts on the **DLP** category.

## Detect Threats

> Unit: https://learn.microsoft.com/en-us/training/modules/microsoft-cloud-app-security/detect-threats

- Built-in **anomaly detection policies** use **UEBA + machine learning**; enabled by default and nondeterministic (fire only on deviation from normal).
- **7-day learning period** builds the baseline (IPs, devices, locations, apps, risk of activities).
- Risk evaluated from **30+ indicators** grouped into factors: risky IP, login failures, admin activity, inactive accounts, location, impossible travel, device/user agent, activity rate.

| Popular anomaly detection | Triggers on |
|---|---|
| Impossible travel | Same user in two locations faster than travel between them allows |
| Activity from infrequent country | Location not recently/ever used by the user or anyone in the org |
| Malware detection | Cloud files matched to known malware via Microsoft threat intelligence |
| Ransomware activity | Cloud uploads possibly infected with ransomware |
| Activity from suspicious IP addresses | IP flagged risky by Microsoft Threat Intelligence |
| Suspicious inbox forwarding | Suspicious forwarding rules on a mailbox |
| Unusual multiple file download activities | Many downloads in one session vs baseline |
| Unusual administrative activities | Many admin actions in one session vs baseline |

- **Discovery anomaly detection policy**: flags unusual increases in app usage (downloaded/uploaded data, transactions, users) vs each app's baseline; filters: app, data views, start date; plus sensitivity.

| Suppression type | Suppresses |
|---|---|
| System | Built-in detections, always suppressed |
| Tenant | Activity common in the tenant (e.g. an ISP already alerted on) |
| User | Activity common for that user (e.g. their usual location) |

| Sensitivity | Suppression applied | Effect |
|---|---|---|
| Low | System + Tenant + User | Fewest alerts |
| Medium | System + User | Balanced |
| High | System only | Most alerts (strictest detection) |

- Infrequent country, anonymous IP, suspicious IP, and impossible travel can evaluate **failed + successful** logins or **successful only**.
- Scoping: Defender portal → **Cloud apps → Policies → Policy management** → Type = **Anomaly detection policy** → Scope: **Specific users and groups** → Include / Exclude → **Update**.
- **Exclude overrides Include** (an excluded user in an included group generates no alerts).
- ⚠️ Exam tip: to stop alerts for a frequent traveler, **scope/exclude** that user in the impossible travel or infrequent country policy rather than disabling it.

## Key terms

| Term | Meaning |
|---|---|
| CASB | Cloud access security broker: policy enforcement point between users and cloud services |
| Shadow IT | Cloud apps in use that IT doesn't know about or hasn't approved |
| Cloud Discovery | Log-based analysis of cloud app usage and risk |
| Sanctioned / Unsanctioned | Approved vs flagged-as-risky app status in Cloud Discovery |
| Conditional Access App Control | Real-time access and session control via Entra CA routing to MDCA |
| IdP | Identity provider (e.g. Entra ID) |
| App connector | API connection that lets MDCA scan and govern an app's data |
| File policy | Policy scanning file content (real time and at rest) with governance actions |
| DCS | Data Classification Services: recommended content inspection method, unified labeling |
| UEBA | User and entity behavioral analytics |
| Anomaly detection policy | Built-in ML/UEBA detection that alerts on deviations from baseline |
| Discovery anomaly detection policy | Alerts on unusual increases in cloud app usage |

## Exam traps

- Cloud Discovery vs Conditional Access App Control: discovery = **after the fact** from logs; CAAC = **real time** during access/session.
- Entra CA vs MDCA policy: Entra decides **who/what/where** gets routed; MDCA session/access policies decide **what is allowed** in the session.
- Block download vs protect on download: blocking stops the file; protecting lets it download but **labels and encrypts** it.
- Sensitivity Low vs High: **Low = more suppression, fewer alerts**; **High = system suppression only, more alerts**.
- Include vs Exclude in scoping: **Exclude wins**.
- Built-in DLP vs DCS: Microsoft recommends **DCS**.
- Anomaly policies are **on by default** but need a **7-day** baseline before alerting reliably.

## Top 3 takeaways

1. MDCA is a CASB with four pillars: Shadow IT discovery, information protection, threat/anomaly detection, and compliance assessment.
2. Cloud Discovery (log-based, risk score 1–10, sanction/unsanction) gives visibility; Conditional Access App Control (Entra CA → MDCA session/access policies) enforces real-time control.
3. File policies protect labeled data, and anomaly detection policies (7-day baseline, tunable sensitivity/suppression/scope) detect threats like impossible travel and ransomware.
