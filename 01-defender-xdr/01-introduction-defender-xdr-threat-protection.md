# Module: Introduction to Microsoft Defender XDR threat protection

> Learning path: [Mitigate threats using Microsoft Defender XDR](https://learn.microsoft.com/en-us/training/paths/sc-200-mitigate-threats-using-microsoft-365-defender/) | Source: [Microsoft Learn module](https://learn.microsoft.com/en-us/training/modules/introduction-microsoft-365-threat-protection/)
> Original time: ~25 min (7 units, excl. assessment) | Read time: ~4 min | Last verified: 2026-09-27

## TL;DR

- Defender XDR is Microsoft's integrated threat protection suite: it correlates signals from **endpoints, identities, email, and apps** into one view of the full attack chain.
- Its value is cross-product: one product detects, others contain (e.g. MDE → Intune → Conditional Access), and shared threat intelligence protects the rest of the estate.
- In a modern SOC, Defender XDR is the main alert source and console for triage and investigation, with Microsoft Sentinel adding broader SIEM context.
- The Microsoft Graph security API gives programmatic access to security data, including running advanced hunting (KQL) queries.

## Exam objectives covered

| Exam domain | Objective (paraphrased) |
|---|---|
| Respond to security incidents | Investigate complex attacks (multi-stage, multi-domain) |
| Respond to security incidents | Investigate Sentinel alerts and incidents (hybrid investigation with Defender XDR + Sentinel) |
| Manage a security operations environment | Automated investigation and response (concept: automated remediation with analyst approval) |
| Perform threat hunting | Create Advanced Hunting queries (via the Graph `runHuntingQuery` method) |

## XDR products at a glance

| Domain | Product | Short name |
|---|---|---|
| Endpoints | Microsoft Defender for Endpoint | MDE |
| Email & collaboration | Microsoft Defender for Office 365 | MDO |
| Identities (on-prem AD) | Microsoft Defender for Identity | MDI |
| Cloud apps | Microsoft Defender for Cloud Apps | MDCA |

## Explore Extended Detection & Response (XDR) response use cases

> Unit: https://learn.microsoft.com/en-us/training/modules/introduction-microsoft-365-threat-protection/2-explore-extended-detection-response-use-cases

**Scenario:** malware arrives via a path MDO doesn't cover (personal email, USB) and infects a device.

| Phase | What happens |
|---|---|
| Detect | MDE detects the malicious payload and raises an alert with threat details for the SOC |
| Contain | MDE reports the raised device risk to **Intune** → Intune compliance policy (using MDE risk level) marks the device **non-compliant** → **Entra ID Conditional Access** blocks access to corporate apps |
| Remediate | MDE remediates via automated remediation, analyst-approved automated remediation, or manual investigation |
| Share intel | MDE feeds attack details to **Microsoft Threat Intelligence**; MDO and Defender for Cloud use those signals to find variants in email, Office collaboration, Azure |
| Restore | After cleanup, MDE signals Intune to lower device risk → compliance updated in Entra ID → Conditional Access restores access |

- While restricted, the user can still browse sites that need no corporate authentication; only corporate resources are blocked.
- The block applies to new resource requests **and** to existing sessions on resources that support **Continuous Access Evaluation (CAE)**.
- ⚠️ Exam tip: know the chain order **MDE → Intune (compliance) → Entra ID Conditional Access**. MDE does not block app access by itself; Conditional Access does.

## Understand Microsoft Defender XDR in a Security Operations Center (SOC)

> Unit: https://learn.microsoft.com/en-us/training/modules/introduction-microsoft-365-threat-protection/3-understand-defender-security-operations-center

| Function | Focus |
|---|---|
| Automation | Near-real-time resolution of well-known, frequently seen incident types |
| Triage (Tier 1) | Fast handling of high-volume known incidents needing quick human judgment; approves automated remediation; escalates anomalies |
| Investigation & incident management (Tier 2) | Escalation point; deeper investigation of fewer, complex, often human-operated multi-stage attacks; documents new alert types for Tier 1; handles non-technical coordination (legal, comms, leadership) |
| Hunt & incident management (Tier 3) | Proactive, hypothesis-driven hunting for threats that evaded detection; tunes alerts and automation; red/purple teams connect here |
| Threat intelligence | Context for all other functions (reactive and proactive research, strategic analysis), often via a threat intelligence platform (TIP) |

- Tiers describe specialized skill sets, not a ranking of value.
- Recommended quality bar: alert streams that require an analyst response should be **~90% true positives**.
- In Microsoft's own SOC experience, **XDR alerts produce most of the high-quality alerts**; the rest come from user reports, classic log-query alerts, and other sources.
- Triage teams usually focus on a few areas: email, endpoint AV alerts (EDR alerts go to investigation), and first response to user reports.
- Typical lifecycle: Tier 1 triages → escalates to Tier 2 (may use Sentinel/SIEM for wider context) → Tier 2 remediates and closes → Tier 3 later reviews closed incidents for common root causes, tuning, and automation opportunities.
- ⚠️ Exam tip: Defender XDR's single console across endpoint, email, and identity is what lets analysts clean up phishing mail, malware, and compromised accounts quickly.

## Explore Microsoft Security Graph

> Unit: https://learn.microsoft.com/en-us/training/modules/introduction-microsoft-365-threat-protection/4-explore-microsoft-security-graph

- **Microsoft Graph** = one programmability model and a single endpoint (`https://graph.microsoft.com`, versions `v1.0` and `beta`) over Microsoft 365, Windows, and Enterprise Mobility + Security data.
- EMS services exposed through Graph include **Defender for Identity, Entra ID, and Intune**.
- **Microsoft Graph security API** acts as a broker: one request is federated to all relevant security providers, and results come back aggregated in a **common schema**.
- Typical uses: correlate alerts from many sources, stream alerts to a SIEM, push threat indicators to Microsoft security tools (alert/block/allow), enrich investigations, automate SecOps.
- `beta` = preview APIs that can change or break without notice; `v1.0` = stable.
- Both versions support advanced hunting through the **`runHuntingQuery`** method, which takes a KQL query (test it in Graph Explorer).

```http
POST https://graph.microsoft.com/v1.0/security/runHuntingQuery
{
  "Query": "DeviceProcessEvents | where InitiatingProcessFileName =~ \"powershell.exe\" | project Timestamp, FileName | take 10"
}
```

- ⚠️ Exam tip: remember the method name `runHuntingQuery` and that it runs **Defender XDR advanced hunting KQL** through Graph.

## Investigate security incidents in Microsoft Defender XDR

> Unit: https://learn.microsoft.com/en-us/training/modules/introduction-microsoft-365-threat-protection/5-investigate-security-incident-defender

- This unit is an interactive cloud guide only: it walks through Defender XDR and Microsoft Sentinel working together to investigate an incident in a **hybrid environment**.
- Takeaway: Defender XDR handles the cross-domain incident; Sentinel adds wider SIEM context (non-Microsoft and on-premises data).
- Hands-on detail is covered later in [Mitigate incidents using Microsoft Defender](https://learn.microsoft.com/en-us/training/modules/mitigate-incidents-microsoft-365-defender/).

## Key terms

| Term | Meaning |
|---|---|
| XDR | Extended Detection and Response: detection and response correlated across endpoints, identity, email, and apps |
| SOC | Security Operations Center |
| CAE | Continuous Access Evaluation: lets access to supporting resources be revoked mid-session |
| Microsoft Threat Intelligence | Microsoft's shared intel system; signals from one product help others detect variants |
| Microsoft Graph security API | Broker API that federates requests to security providers and returns results in a common schema |
| `runHuntingQuery` | Graph security method that runs an advanced hunting KQL query |
| TIP | Threat Intelligence Platform |

## Exam traps

- Device risk vs access block: MDE *reports* risk; **Intune** sets compliance; **Conditional Access** enforces the block.
- Triage vs investigation: endpoint **AV** alerts are usually triage (Tier 1); **EDR/behavioral** alerts go to investigation (Tier 2).
- Hunting (Tier 3) is **hypothesis-driven and proactive**, not alert-driven.
- Graph `beta` is preview and can change without notice; `v1.0` is the stable production version. Both support `runHuntingQuery`.

## Top 3 takeaways

1. Defender XDR correlates MDE, MDO, MDI, and MDCA signals into one attack-chain view and one console.
2. Cross-product containment: MDE risk → Intune non-compliance → Conditional Access block, restored automatically after remediation.
3. The Graph security API (single endpoint, common schema) enables automation and advanced hunting via `runHuntingQuery`.
