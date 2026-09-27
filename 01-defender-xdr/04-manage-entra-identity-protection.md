# Module: Manage Microsoft Entra Identity Protection

> Learning path: [Mitigate threats using Microsoft Defender XDR](https://learn.microsoft.com/en-us/training/paths/sc-200-mitigate-threats-using-microsoft-365-defender/) | Source: [Microsoft Learn module](https://learn.microsoft.com/en-us/training/modules/manage-azure-active-directory-identity-protection/)
> Original time: ~52 min (10 units, excl. assessment) | Read time: ~5 min | Last verified: 2026-09-27

## TL;DR

- Microsoft Entra ID Protection detects identity-based risk (users, sign-ins, workload identities), automates the response through risk policies, and exposes risk data for investigation and export (portal, Graph API, SIEM).
- Three policies: **user risk** (account is probably compromised), **sign-in risk** (this sign-in probably wasn't the user), and **MFA registration**. All require **Entra ID P2**.
- Analysts investigate in three reports (**Risky users, Risky sign-ins, Risk detections**) and remediate by self-remediation, password reset, dismissing risk, or closing detections.
- Coverage extends to **workload identities** (apps, service principals, managed identities), and the LLM-based **Identity Risk Management Agent** investigates risky users and suggests fixes.
- **Defender for Identity** complements it by covering on-premises Active Directory signals.

## Exam objectives covered

| Exam domain | Objective (paraphrased) |
|---|---|
| Respond to security incidents | Investigate and remediate compromised identities identified by Microsoft Entra ID |
| Respond to security incidents | Investigate Defender for Identity alerts (intro only: MDI architecture) |
| Respond to security incidents | Investigate with agentic AI (Identity Risk Management Agent in Entra ID Protection) |

## Review identity protection basics

> Unit: https://learn.microsoft.com/en-us/training/modules/manage-azure-active-directory-identity-protection/2-review-identity-protection-basics

- Three core jobs: automate detection and remediation of identity risk, investigate risk in the portal, export risk data to third-party tools.
- Risk signals can feed **Conditional Access** (access decisions) and a **SIEM** (further investigation).
- ⚠️ Exam tip: Identity Protection needs **Microsoft Entra ID P2**.

**Risk detections**

| Detection | What it flags | Detected by |
|---|---|---|
| Anonymous IP address | Sign-in from an anonymizer (Tor, anonymizer VPN) | Entra ID Protection |
| Atypical travel | Sign-in location unusual versus the user's recent sign-ins | Entra ID Protection |
| Malicious IP address | Sign-in from a known malicious IP | Entra ID Protection |
| Unfamiliar sign-in properties | Sign-in properties not seen recently for this user | Entra ID Protection |
| Leaked credentials | User's valid credentials have been exposed | Entra ID Protection |
| Password spray | Many usernames attacked with common passwords | Entra ID Protection |
| Microsoft Entra threat intelligence | Matches a known attack pattern from Microsoft intel sources | Entra ID Protection |
| Anomalous token | Unusual token traits (odd lifetime, replay from unfamiliar location) | Entra ID Protection |
| Token issuer anomaly | SAML token issuer possibly compromised | Entra ID Protection |
| Suspicious browser | Anomalous sign-ins across multiple tenants from one browser | Entra ID Protection |
| Verified threat actor IP | Sign-in from IPs tied to verified threat actors | Entra ID Protection |
| New country | Activity from a new country | **MDCA** |
| Activity from anonymous IP address | Activity from an anonymous IP | **MDCA** |
| Suspicious inbox forwarding | Suspicious mail-forwarding rule | **MDCA** |

**Roles** (access requires Security Reader, Security Operator, Security Administrator, or Global Reader)

| Role | Can | Can't |
|---|---|---|
| Security Administrator | Full access | Reset user passwords |
| Security Operator | View reports and Overview; dismiss user risk; confirm safe sign-in; confirm compromise | Change policies, reset passwords, configure alerts |
| Security Reader | View reports and Overview | Change policies, reset passwords, configure alerts, give feedback on detections |

- Per the module, Security Operator can't open the **Risky sign-ins** report.
- **Conditional Access Administrators** can build CA policies that use sign-in risk as a condition.
- ⚠️ Exam tip: even Security Administrator can't reset a user's password from Identity Protection.

**Licensing**

| Capability | Free / M365 Apps | P1 | P2 |
|---|---|---|---|
| User risk policy, sign-in risk policy | No | No | Yes |
| Overview report | No | No | Yes |
| Risky users report | Medium/high users only; no details drawer or history | Same as Free | Full |
| Risky sign-ins report | No risk detail or risk level | Same as Free | Full |
| Risk detections report | No | Limited; no details drawer | Full |
| Users-at-risk alerts, weekly digest | No | No | Yes |
| MFA registration policy | No | No | Yes |

## Implement and manage user risk policy

> Unit: https://learn.microsoft.com/en-us/training/modules/manage-azure-active-directory-identity-protection/3-implement-manage-user-risk-policy

| Policy | Evaluates | Microsoft-recommended threshold |
|---|---|---|
| Sign-in risk policy | Probability that the sign-in wasn't performed by the user | **Medium and above** |
| User risk policy | Probability that the account is compromised (behavior atypical for the user) | **High** |

- Both policies automate the response and let users **self-remediate**.
- Self-remediation prerequisite: users registered for **both SSPR and MFA**; combined security info registration is recommended.
- Threshold trade-off: higher = fewer prompts but low/medium risk goes unchallenged; lower = more prompts, stronger posture.
- Exclude emergency access (break-glass) accounts; review all exclusions regularly.
- Configured **trusted network locations** reduce false positives in some detections.

## Exercise enable sign-in risk policy

> Unit: https://learn.microsoft.com/en-us/training/modules/manage-azure-active-directory-identity-protection/4-exercise-enable-sign-risk-policy

- Location: **Entra admin center > Identity > Protection > Identity Protection > User risk policy / Sign-in risk policy**.
- Policy structure: Assignments (all users or selected users/groups, plus exclusions) → risk-level condition → access control → Enforce **On**.
- User risk policy: Microsoft recommends **Allow access + Require password change**.
- Sign-in risk policy: control used is **Require multifactor authentication**.

## Exercise configure Microsoft Entra multifactor authentication registration policy

> Unit: https://learn.microsoft.com/en-us/training/modules/manage-azure-active-directory-identity-protection/5-exercise-configure-multi-factor-authentication-registration-policy

- Purpose: make users register for MFA so they can answer MFA prompts (including risk-driven ones) later.
- Same location: Identity Protection > **Multifactor authentication registration policy**; assign users/groups with exclusions.
- The control **Require Microsoft Entra ID MFA registration** is fixed and can't be changed; set Enforce to **Enabled**.

## Monitor, investigate, and remediate elevated risky users

> Unit: https://learn.microsoft.com/en-us/training/modules/manage-azure-active-directory-identity-protection/6-monitor-investigate-remediate-elevated-risky-users

- Reports live in **Entra admin center > Identity > Protection > Identity Protection**; columns and filters are configurable; export as CSV or JSON.

| Report | Shows | Data window | Export limit | Admin actions |
|---|---|---|---|---|
| Risky users | Users at risk / remediated / dismissed, detection details, risky sign-in history, risk history | — | Latest 2,500 | Reset password, confirm compromised, dismiss risk, block sign-in, investigate in MDI |
| Risky sign-ins | Sign-in risk state, real-time and aggregate risk level, detection types, CA policies applied, MFA, device, app, location | **30 days** | Latest 2,500 | Confirm sign-in compromised, confirm sign-in safe |
| Risk detections | Each detection and type, risks raised at the same time, location; link to the detection in MDCA | **90 days** | Latest **5,000** | None directly; pivot to user or sign-in report |

- **User risk level** (low/medium/high) is calculated from all **active** risk detections; close detections as fast as possible.
- **Closed (system)**: Identity Protection decided the event is no longer risky.
- **AI confirmed sign-in safe**: the system auto-dismissed the risk because it was a false positive or the user remediated via policy (MFA or secure password change).

**Remediation options**

| Option | Effect | Condition / caveat |
|---|---|---|
| Self-remediation via risk policy | User passes MFA / SSPR; detections closed | User must already be registered for MFA and SSPR |
| Password reset: generate temporary password | Identity immediately back to safe state | Admin must contact the user; change forced at next sign-in |
| Password reset: require user to reset | Self-recovery, no help desk | Only for users registered for MFA and SSPR |
| Dismiss user risk | Closes all events; user no longer at risk | Password unchanged, so identity is **not** returned to a safe state (e.g. use for a deleted user) |
| Close individual detections | Lowers user risk level | Options: confirm user compromised, dismiss user risk, confirm sign-in safe, confirm sign-in compromised |

**Unblocking**

| Blocked because of | Options |
|---|---|
| User risk | Reset password; dismiss user risk or close detections; exclude user from policy; disable policy |
| Sign-in risk | Sign in from a familiar location or device; exclude user from policy; disable policy |

- Risk can also be managed with the Microsoft Graph PowerShell SDK preview module (samples in the [IdentityProtectionTools repo](https://github.com/AzureAD/IdentityProtectionTools)).

**Microsoft Graph APIs**

| API | Returns |
|---|---|
| `riskDetection` | User- and sign-in-linked risk detections |
| `riskyUsers` | Users Identity Protection flagged as risky |
| `signIn` | Entra ID sign-ins with risk state, detail, and level |

- Access sequence: note tenant `.onmicrosoft.com` domain → register an app → add **application** permissions `IdentityRiskEvent.Read.All` and `IdentityRiskyUser.Read.All` + grant admin consent → add a credential → get a token (client credentials) → call Graph.
- Security: keep secrets out of code; the module points to managed identities for Azure resources.

```http
GET https://graph.microsoft.com/v1.0/identityProtection/riskDetections?$filter=detectionTimingType eq 'offline'
GET https://graph.microsoft.com/v1.0/identityProtection/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

- ⚠️ Exam tip: **offline** detections are found after the sign-in, so the sign-in risk policy never evaluated them; query them with `detectionTimingType eq 'offline'`.
- ⚠️ Exam tip: `userPassedMFADrivenByRiskBasedPolicy` identifies users who passed a risk-triggered MFA challenge (useful for spotting false positives).

## Implement security for workload identities

> Unit: https://learn.microsoft.com/en-us/training/modules/manage-azure-active-directory-identity-protection/7-implement-security-workload-identities

- Workload identity = identity that lets an app or service principal access resources (sometimes in a user's context); covers apps, service principals, managed identities.
- Riskier than user accounts: can't do MFA, often no formal lifecycle, must store credentials/secrets.
- Requirements (per module): **Entra ID P2** + Security Administrator, Security Operator, or Security Reader.
- Portal: **Risky workload identities** blade and **Workload identity detections** tab in Risk detections.

| Detection (all offline) | What it flags |
|---|---|
| Microsoft Entra threat intelligence | Activity matching known attack patterns |
| Suspicious sign-ins | Unfamiliar IP/ASN, target resource, user agent, hosting change, country, or credential type vs a 2–60 day baseline |
| Unusual addition of credentials to an OAuth app | Suspicious privileged credential added (detected by **MDCA**) |
| Admin confirmed account compromised | Admin chose Confirm compromised in UI or `riskyServicePrincipals` API (risk history shows who) |
| Leaked credentials | Valid credentials exposed (e.g. public GitHub code, data breach) |

- **Conditional Access for workload identities** can block accounts Identity Protection marks at risk.
- CA scope: **single-tenant service principals registered in your tenant** only; third-party SaaS, multitenant apps, and managed identities are out of scope.
- ⚠️ Exam tip: managed identities get risk detection but **can't** be targeted by CA for workload identities.

## Explore Microsoft Defender for Identity

> Unit: https://learn.microsoft.com/en-us/training/modules/manage-azure-active-directory-identity-protection/8-explore-microsoft-defender-identity

- MDI (formerly Azure ATP): cloud service that uses **on-premises AD** signals to detect advanced threats, compromised identities, and malicious insiders in hybrid environments.
- Capabilities: learning-based behavior analytics, protect AD-stored credentials, investigate activity across the kill chain, incident timeline for fast triage.

| Component | Role |
|---|---|
| Microsoft Defender portal (`security.microsoft.com`) | Manage MDI, monitor and investigate sensor data |
| MDI sensor | Installed on **domain controllers** (no dedicated server or port mirroring) and **AD FS** servers |
| MDI cloud service | Runs on Azure (US, Europe, Asia); connected to Microsoft threat intelligence |

- ⚠️ Exam tip: Entra ID Protection = cloud identity risk; MDI = on-prem AD threats. The Risky users report can pivot to **Investigate in MDI**.

## Explore the Identity Risk Management Agent

> Unit: https://learn.microsoft.com/en-us/training/modules/manage-azure-active-directory-identity-protection/8a-identity-risk-management-agent

- LLM-based agent in Entra ID Protection: investigates risky users, summarizes risk, and suggests remediation before incidents occur.
- Prerequisites: **Entra ID P2**, available **security compute units (SCUs)**, and a suitable role.

| Role | Agent permissions |
|---|---|
| Security Administrator | Activate agent (first time), view, act on suggestions |
| Security Reader, Global Reader | View agent and suggestions only |

| Phase | Steps | SCUs |
|---|---|---|
| Detect | Find new users with risk state **At risk** inside the configured scope | Not consumed |
| Suggest | Investigate sign-ins + detections → findings and risk summary → recommended action → chat Q&A → store custom instructions in memory (preferred remediation actions) | **Consumed** |

- Start: **Entra admin center > ID Protection > Risky users** → banner → **Start agent**; settings via Agent view > ellipsis > Settings.

| Setting | Options / defaults |
|---|---|
| Triggers | Continuous (checks every **5 min**), daily, manual |
| Scope | Default **100 most recent risky users in last 90 days**; users/groups; max 1–100; risk levels (all by default); 7 / 14 / 30 days or custom up to 90 |
| Controls | Roles and permissions to run the agent |
| Communications | Users notified of agent runs |
| Memory | User-confirmed safe items (false positives) |

- Output: agent summary tile (chat, manage/run agent); suggestions with **bulk action**; per-user verdict **Compromised / Not compromised**, risk summary, risk factors, suggested action.
- Remediation actions currently available: **Dismiss risk**, **Reset password**.
- ⚠️ Exam tip: SCUs are only consumed when the agent generates suggestions, not when it checks for new risky users.

## Key terms

| Term | Meaning |
|---|---|
| Entra ID Protection | Entra ID service that detects, investigates, and remediates identity-based risk (P2) |
| User risk | Likelihood an account is compromised; level computed from active detections |
| Sign-in risk | Likelihood a specific sign-in wasn't made by the account owner |
| Risk detection | Individual risky event (e.g. leaked credentials, atypical travel) |
| Offline detection | Detection found after the sign-in, so not evaluated by the sign-in risk policy |
| MFA registration policy | Identity Protection policy forcing users to register for MFA |
| Self-remediation | User clears their own risk by passing MFA or SSPR as required by policy |
| Dismiss user risk | Closes all user risk events without changing the password |
| Closed (system) | Detection closed by Identity Protection because it's no longer considered risky |
| Workload identity | Identity used by an app, service principal, or managed identity |
| CA for workload identities | Conditional Access blocking at-risk single-tenant service principals |
| MDI | Microsoft Defender for Identity: on-prem AD threat detection (formerly Azure ATP) |
| Identity Risk Management Agent | LLM agent in Entra ID Protection that investigates risky users and suggests remediation |
| SCU | Security compute unit, capacity consumed by the agent when generating suggestions |
| SSPR | Self-service password reset |

## Exam traps

- User risk vs sign-in risk: user risk = the **account** is compromised (recommended **High**, require password change); sign-in risk = this **sign-in** is suspicious (recommended **Medium and above**, require MFA).
- Dismiss user risk vs password reset: dismiss closes events but leaves the password, so the identity is **not** made safe.
- Risky sign-ins vs risk detections retention: **30 days** vs **90 days**; export limits **2,500** (users, sign-ins) vs **5,000** (detections).
- Security Operator vs Security Reader: Operator can dismiss risk and confirm safe/compromised; Reader is view-only. Neither can change policies or reset passwords.
- CA for workload identities: single-tenant registered service principals only; **not** managed identities, multitenant apps, or third-party SaaS.
- Some detections (New country, Activity from anonymous IP, Suspicious inbox forwarding, Unusual credential addition to OAuth app) come from **MDCA**, not Entra ID Protection itself.
- Entra ID Protection vs MDI: cloud identity/sign-in risk vs on-prem AD traffic via sensors on DCs and AD FS.

## Top 3 takeaways

1. Entra ID Protection (P2) scores user and sign-in risk and enforces it through user risk, sign-in risk, and MFA registration policies, ideally with MFA + SSPR self-remediation.
2. Investigate in Risky users, Risky sign-ins (30 days), and Risk detections (90 days); remediate by self-remediation, password reset, dismissing risk, or closing detections, knowing dismiss doesn't make the identity safe.
3. Protection extends to workload identities (CA only for single-tenant service principals) and to an SCU-based agent that investigates risky users and suggests Dismiss risk or Reset password.
