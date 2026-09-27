# Module: Safeguard your environment with Microsoft Defender for Identity

> Learning path: [Mitigate threats using Microsoft Defender XDR](https://learn.microsoft.com/en-us/training/paths/sc-200-mitigate-threats-using-microsoft-365-defender/) | Source: [Microsoft Learn module](https://learn.microsoft.com/en-us/training/modules/m365-threat-safeguard/)
> Original time: ~64 min (5 units, excl. assessment) | Read time: ~5 min | Last verified: 2026-09-27

## TL;DR

- Microsoft Defender for Identity (MDI) is a cloud-based service that uses identity signals from **on-premises AD, Entra ID, and other identity providers** to detect advanced threats, compromised identities, and malicious insiders.
- **Sensors** on domain controllers (and AD FS, AD CS, Entra Connect servers) parse DC traffic and Windows events locally and send only the parsed data to the MDI cloud service.
- MDI alerts follow the attack kill chain (reconnaissance → compromised credentials → lateral movement → domain dominance → exfiltration) and appear in the Defender portal alert queue.
- MDI is a native Defender XDR workload: its alerts are auto-correlated with MDE and Defender for Cloud Apps alerts into shared incidents, with no separate integration step.

## Exam objectives covered

| Exam domain | Objective (paraphrased) |
|---|---|
| Respond to security incidents | Investigate and remediate threats identified by Defender for Identity alerts |
| Respond to security incidents | Investigate complex attacks (multi-stage, multi-domain, lateral movement) |
| Respond to security incidents | Investigate with agentic AI, incl. Security Copilot (MDI insights feed Security Copilot; mention only) |

## Introduction to Microsoft Defender for Identity

> Unit: https://learn.microsoft.com/en-us/training/modules/m365-threat-safeguard/introduction

| Capability | What it does |
|---|---|
| Behavior profiling | Learns a per-user baseline (activity, permissions, group membership) and flags anomalies with learning-based analytics |
| Posture assessment | Surfaces identity config weaknesses; results feed **Microsoft Secure Score** |
| Lateral Movement Paths (LMPs) | Visualize how an attacker could move from a low-privileged account to sensitive accounts, so you can close paths proactively |
| Security reports | Highlight risky config, e.g. users/devices authenticating with **clear-text passwords** |
| Kill-chain detections | Detect suspicious activity from reconnaissance to domain dominance |
| Incident timeline | Presents incident info on a simple timeline for fast triage |

Example detections by kill-chain stage:

| Stage | Example | Detection trigger |
|---|---|---|
| Reconnaissance | LDAP reconnaissance | Suspicious LDAP enumeration or queries targeting sensitive groups |
| Compromised credentials | Brute force / password spray | Multiple authentication failures via **Kerberos or NTLM**, or a password spray pattern |
| Lateral movement | Pass-the-ticket | Same Kerberos ticket used on **2+ different computers** |
| Domain dominance | DCShadow | A machine tries to register as a **rogue domain controller** to push changes via malicious replication |

- ⚠️ Exam tip: DCShadow = rogue DC + malicious replication, and it can be launched from any machine, not only a real DC.

## Configure Microsoft Defender for Identity sensors

> Unit: https://learn.microsoft.com/en-us/training/modules/m365-threat-safeguard/configure-sensors

- On a DC, the sensor reads required event logs directly; it sends only parsed information to the cloud, not full raw logs.
- Core sensor functions: capture local DC network traffic, receive Windows events, receive **RADIUS accounting** from VPN providers, query AD for user/computer data, resolve network entities, forward relevant data to the cloud.
- Portal location: **Defender portal > Settings > Identities > Deployment: On-premises > Sensors** tab (list, filter, **Export** to .csv).
- Sensor types listed: domain controller, AD FS, standalone, Entra Connect, AD CS.
- Sensor list also shows version, delayed-update setting, health status/issues, and **migration state** (eligibility to move from sensor v2.x to v3.x).

| Setting (Manage sensor) | Requirement |
|---|---|
| Description | Optional |
| Domain Controllers (FQDN) | Required for **standalone** sensors and sensors on **AD FS / AD CS**; cannot be changed for a DC-installed sensor. List every DC monitored via port mirroring; **at least one must be a global catalog** (to resolve objects in other forest domains) |
| Capture Network adapters | Required. DC sensor: adapters used to talk to other machines. Standalone sensor: the adapters set as the **destination mirror port** |

Validate the installation:

| Check | How |
|---|---|
| Deployment | Service **Azure Advanced Threat Protection sensor** is running; if not, check `Microsoft.Tri.sensor-Errors.log` under `%programfiles%\Azure Advanced Threat Protection sensor\<version>\Logs` |
| Alert functionality | From a domain-joined device, run `nslookup` → `server <DC FQDN>` → `ls -d <domain>`, then check the device **Timeline** for action type **MdiDnsQuery** |
| First sensor | Wait **at least 15 minutes** before checking activity (backend initial deployment) |
| Latest version | **Settings > Identities > About** |

- AD FS / AD CS sensors use a different set of validation steps.
- ⚠️ Exam tip: the sensor's Windows service still carries the legacy name **Azure Advanced Threat Protection sensor**.

## Review compromised accounts or data

> Unit: https://learn.microsoft.com/en-us/training/modules/m365-threat-safeguard/review-compromised-accounts

- MDI alert phases: **reconnaissance, compromised credential, lateral movement, domain dominance, exfiltration**.
- Each alert contains: title (official MDI name), description, evidence (with links to involved users and computers), and an Excel report download.
- Portal location: **Defender portal > Incidents & alerts > Alerts** (shared queue with Defender for Cloud Apps, MDE, and other workloads).
- Alerts also include an **activity log** showing details such as the command that was run.

Walkthrough attack chain (order matters):

| # | Alert / step | What the attacker did |
|---|---|---|
| 1 | User and IP address reconnaissance (SMB) | Enumerated SMB sessions on the DC to learn other users' IPs, incl. a domain admin's PC |
| 2 | Suspected overpass-the-hash attack (Kerberos) | Used an NTLM hash stolen from a user who had logged on to the infiltrated PC; account was on a lateral movement path |
| 3 | Suspected identity theft (pass-the-ticket) | Stole the domain admin's Kerberos ticket; portal shows resources accessed with it |
| 4 | Remote code execution attempt | Ran a remote command on the DC with stolen credentials |
| 5 | Activity log | Command created a new user in the **Administrators** group → domain compromised |

- Once domain admin rights are gained, further attacks such as **Skeleton Key** become possible.
- ⚠️ Exam tip: recognize the pattern recon → credential theft → lateral movement → domain dominance and map each MDI alert to its phase.

## Integrate with other Microsoft tools

> Unit: https://learn.microsoft.com/en-us/training/modules/m365-threat-safeguard/integrate-microsoft-tools

- MDI is native to Defender XDR: on-premises AD and Entra ID signals feed correlated incidents in the unified Defender portal.
- MDI, Defender for Cloud Apps, and MDE alerts are **automatically correlated** into incidents; no separate integration setup.
- MDI watches DC traffic; MDE watches endpoints. Selecting a device in the portal shows the MDI alerts tied to it.
- Walkthrough: a high-severity MDE alert shows a **pass-the-hash** attack with **Mimikatz**, with an event timeline around the credential theft.

| Other integration | Purpose |
|---|---|
| PAM platforms (CyberArk, Delinea, BeyondTrust) | Detect threats against privileged accounts those platforms manage |
| Okta | Monitor Okta sign-ins and activity in hybrid identity environments |
| Microsoft Security Copilot | MDI identity insights support natural-language triage and investigation |

- ⚠️ Exam tip: MDI = domain controller / identity signals; MDE = endpoint process and device signals. Both land in the same incident queue.

## Key terms

| Term | Meaning |
|---|---|
| MDI | Microsoft Defender for Identity: identity threat detection for on-prem AD, Entra ID, and other IdPs |
| MDI sensor | Agent on DC / AD FS / AD CS / Entra Connect servers (or standalone) that parses traffic and events and sends results to the cloud |
| Standalone sensor | Sensor on a dedicated server that receives DC traffic through port mirroring |
| Lateral Movement Path (LMP) | Visual path showing how an attacker could reach sensitive accounts |
| LDAP reconnaissance | Enumeration of the directory to map domain structure and privileged accounts |
| Password spray | One password tried against many accounts |
| Pass-the-ticket | Reusing a stolen Kerberos ticket on another computer |
| Overpass-the-hash | Using a stolen NTLM hash to obtain Kerberos access |
| Pass-the-hash (PtH) | Authenticating with a stolen password hash (e.g. via Mimikatz) |
| DCShadow | Registering a rogue DC to change directory objects via malicious replication |
| Skeleton Key | Post-compromise attack named as a follow-on to domain dominance |
| RADIUS accounting | VPN session data the sensor can ingest |
| MdiDnsQuery | Device timeline action type used to confirm the sensor detects DNS activity |

## Exam traps

- Kill chain in the intro has 4 stages, but alert categories have **5** (adds **exfiltration**).
- Pass-the-ticket (stolen **Kerberos ticket** reused) vs overpass-the-hash (stolen **NTLM hash** turned into Kerberos access) vs pass-the-hash (hash used directly).
- DC FQDN list: required for **standalone, AD FS, AD CS** sensors; **not editable** on a DC sensor; needs at least one **global catalog**.
- Standalone sensor capture adapters = the **mirror destination** ports, not the server's normal NIC.
- Sensor sends **parsed** data, not all raw logs.
- Sensor settings live under **Settings > Identities**, not under Endpoints.
- Posture findings are surfaced through **Secure Score**.

## Top 3 takeaways

1. MDI detects identity attacks across the kill chain (recon, credential theft, lateral movement, domain dominance, exfiltration) using sensors on DCs and identity servers.
2. Configure sensors in Settings > Identities > Sensors; standalone/AD FS/AD CS sensors need a DC FQDN list with a global catalog; validate via the service, the error log, and MdiDnsQuery in the device timeline.
3. MDI alerts correlate automatically with MDE and Defender for Cloud Apps into shared Defender XDR incidents, and extend to PAM platforms, Okta, and Security Copilot.
