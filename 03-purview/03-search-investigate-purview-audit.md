# Module: Search and investigate with Microsoft Purview Audit

> Learning path: [Mitigate threats using Microsoft Purview](https://learn.microsoft.com/en-us/training/paths/sc-200-mitigate-threats-using-microsoft-purview/) | Source: [Microsoft Learn module](https://learn.microsoft.com/en-us/training/modules/purview-audit-search-investigate/)
> Original time: ~52 min (9 units, excl. assessment) | Read time: ~5 min | Last verified: 2026-09-27

## TL;DR

- Purview Audit records user and admin activity across Microsoft 365 (mail, files, admin changes, Copilot/AI apps) in one **unified audit log** you search after the fact.
- **Audit (Standard)** is on by default with 180-day retention; **Audit (Premium)** adds retention policies (up to 10 years), 1-year default for key workloads, and high-value events like **`MailItemsAccessed`**.
- Records exist only from enablement onward (tenant and, for Premium events, per user); nothing is backfilled.
- Search via the Purview portal, `Search-UnifiedAuditLog` (Exchange Online PowerShell), or APIs; export to CSV and parse the `AuditData` JSON to build findings.
- Audit tells you *that* something happened, not the content; pair it with eDiscovery, DLP, DSPM, IRM, Defender, and Sentinel.

## Exam objectives covered

| Exam domain | Objective (paraphrased) |
|---|---|
| Respond to security incidents | Investigate threats with Microsoft Purview Audit |
| Respond to security incidents | Investigate compromised entities identified by Purview (supporting: compromised-mailbox investigation with `MailItemsAccessed`) |

## Understand how Microsoft Purview Audit works

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-audit-search-investigate/audit-overview

- **Record type** = activity category (e.g. `SharePointFileOperation`, `Copilot`); **Operation** = specific action (e.g. `FileAccessed`, `CopilotInteraction`).
- Exchange, SharePoint, OneDrive, Teams, Entra ID, and Copilot all write to the **same unified audit log**.
- Coverage varies by workload: an empty result doesn't prove the activity didn't happen.
- Access points: Purview portal, `Search-UnifiedAuditLog`, Office 365 Management Activity API, Audit Search Graph API.

| Capability | Audit (Standard) | Audit (Premium) |
|---|---|---|
| Search user/admin activity | ✔ | ✔ |
| Default retention | 180 days | 180 days; **1 year** for Entra ID, Exchange, SharePoint, OneDrive |
| Retention policies (up to 10 years) | – | ✔ |
| High-value events (`MailItemsAccessed`, etc.) | – | ✔ |
| Higher Management Activity API bandwidth | – | ✔ |

- Audit is **not** for real-time monitoring, alerting, holds, blocking actions, or risk scoring.
- ⚠️ Exam tip: message-level "which email did they open?" questions need `MailItemsAccessed` → Premium only.

## Configure and manage Microsoft Purview Audit

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-audit-search-investigate/audit-configure-manage

- Auditing is **on by default**; verify with `Get-AdminAuditLogConfig | Format-List UnifiedAuditLogIngestionEnabled`.
- Enable: portal banner in **Audit** or `Set-AdminAuditLogConfig -UnifiedAuditLogIngestionEnabled $true`; takes up to 60 min.
- **Disable only via PowerShell** (`... $false`).
- PowerShell uses the `ExchangeOnlineManagement` module + `Connect-ExchangeOnline`.

| Role group | Can do |
|---|---|
| Audit Manager | Search + export + manage audit settings (incl. turn logging on/off) |
| Audit Reader | Search + export only |

- Both groups contain the **Audit Logs** / **View-Only Audit Logs** roles required to search.
- Premium events (`MailItemsAccessed`, `Send`, `SearchQueryInitiatedExchange`, `SearchQueryInitiatedSharePoint`) need the **Microsoft 365 Advanced Auditing** service plan enabled **per user** (M365 admin center → Users → Licenses and apps).
- Per the unit, E5 / Office 365 E5 / E5 Compliance include the plan but it isn't switched on automatically.
- Propagation ~15–30 min; full logging up to 24 h; records start from enablement date.
- If mailbox audit actions were customized earlier, new Premium events aren't added: re-add with `Set-Mailbox <user> -AuditOwner @{Add="SearchQueryInitiated"}` (multi-geo: run in the mailbox's region).
- **Administrative units** scope search: unrestricted admins see all logs (incl. non-user/system accounts); restricted admins see only users in their admin units.
- Some activities (e.g. Endpoint DLP file events, Exchange `Set-Mailbox`) are visible only to unrestricted admins or via `Search-UnifiedAuditLog`.
- ⚠️ Exam tip: "E5 purchased but no `MailItemsAccessed`" → Advanced Auditing not enabled on the user.

## Run audit searches with Audit (Standard)

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-audit-search-investigate/audit-standard-search

- Portal: **Purview portal → Solutions → Audit**.
- Filters: date range (default last 7 days), keyword (`*` wildcard), admin units, activities, record types, users, file/folder/site, workloads, search name.
- Limits: **10 concurrent search jobs per user**, only **1 unfiltered** job.
- Jobs run in the background; large searches can take up to **48 h**; delete and narrow if stuck (0% > 1 h or no progress in a day).
- Completed jobs are kept **30 days**; **Copy this search** duplicates parameters; deleting a job doesn't touch audit data.
- Result columns: Date (UTC), IP address, user, record type, activity, item, admin units, details.
- Latency: core services **60–90 min**; others can be slower.
- All timestamps are **UTC** (portal and PowerShell).
- Non-user activity (service principals, app permissions, system events) is capped at **1 year** regardless of tier or policy.

**Empty result checklist:** auditing on → date inside retention → activity old enough to be indexed → Advanced Auditing on the user during the window (Premium events) → role/admin-unit scope → dates in UTC.

- Deleted-mail scenario: filter Exchange mailbox activities (deleted from Deleted Items, purged from mailbox) and check **Details → More information** for subject and original location.
- Soft-deleted items are recoverable from Recoverable Items until the deleted-item retention limit; hard-deleted items may be recoverable if the mailbox is on hold or under a retention policy.
- ⚠️ Exam tip: "zero results for local Wednesday" → check UTC conversion first.

## Search for Copilot and AI app activities

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-audit-search-investigate/audit-copilot

- Audit stores Copilot/AI **metadata** (who, when, where, which resources), not prompt/response text; use DSPM, Communication Compliance, or eDiscovery for content.

| Record type | Workload | Covers | Depends on |
|---|---|---|---|
| `CopilotInteraction` | Copilot | First-party Copilot (M365 Copilot, Copilot Chat, Security Copilot, Copilot in Fabric) | Auditing only |
| `ConnectedAIAppInteraction` | ConnectedAIApp | Copilot Studio agents + Entra-registered non-Microsoft AI apps | Entra registration + **DSPM onboarding** |
| `AIAppInteraction` | AIApp | Non-Microsoft AI apps used outside the tenant (browser/network) | **Purview Browser Extension + DLP** policy on AI traffic |

- `AIAppInteraction` and non-Microsoft Entra-registered app records need **pay-as-you-go billing** (tenant linked to an Azure subscription); Copilot Studio agents are excluded.
- Admin changes use separate operations: `UpdateTenantSettings`, `CreatePlugin`, `DeletePlugin`, `EnablePromptBook`.

| Question | Property |
|---|---|
| Which app? | `AppIdentity` (`workloadName.appGroup.appName`) |
| Which files/sites were read? | `AccessedResources` (incl. `SensitivityLabelId`) |
| Jailbreak attempt? | `JailbreakDetected` flag in `Messages` |
| Where was the user? | `AppHost` (e.g. `BizChat`, `Bing`, `Office`, `Word`) |
| What were they doing? | `Contexts` |
| Copilot Studio agent involved? | `AgentName`, `AgentVersion` |

- Portal: Activities → **Copilot activities**, or Record types → the three types above; same 60–90 min latency.
- To filter by `AppIdentity`, export by operation first, then filter in Excel/Power Query.
- ⚠️ Exam tip: known Copilot Studio agent but no records → agent not onboarded through DSPM.

## Investigate activities with Audit (Premium)

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-audit-search-investigate/audit-premium-investigate

| `MailAccessType` | Meaning |
|---|---|
| Sync | One event when a client (e.g. Outlook) downloads many items in a session |
| Bind | One event per individual message opened |

- Throttling: > **1,000 bind events in 24 h** on a mailbox pauses `MailItemsAccessed` bind logging (<1% of mailboxes; sync and other events unaffected).
- Throttled gaps **don't backfill**: reconstruct with **Exchange message trace** or **Entra sign-in logs**; `SearchQueryInitiated*` events aren't throttled.
- Check for gaps via `IsThrottled = True` in `AuditData`; identify opened messages via `InternetMessageId`.
- Deduplication: repeat `MailItemsAccessed` records are filtered within **1 hour** unless context changes (`ClientIPAddress`, `SessionId`, `MailAccessType`).
- Compare IP addresses and session IDs to separate normal from suspicious access.
- `-StartDate`/`-EndDate` are UTC; `-ResultSize` counts events, not mail items.
- `Search-MailboxAuditLog` is being deprecated in Exchange Online → use `Search-UnifiedAuditLog`.

```powershell
Search-UnifiedAuditLog -StartDate "2026-01-06" -EndDate "2026-01-20" -UserIds "user1" -Operations MailItemsAccessed -ResultSize 1000
```

| Other Premium event | Answers |
|---|---|
| `Send` | Did the (compromised) account send or reply to mail? |
| `SearchQueryInitiatedExchange` / `SearchQueryInitiatedSharePoint` | What was the user searching for in mail / SharePoint? |

- ⚠️ Exam tip: all these events require Advanced Auditing on the user and only exist from its enablement date.

## Analyze and report on audit results

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-audit-search-investigate/audit-export

| Export path | Row limit |
|---|---|
| Portal export (Standard) | 50,000 per export |
| Portal export (Premium) | 1,000,000 per export |
| `Search-UnifiedAuditLog` per call | 100 default, **5,000 max** (page with `-SessionId` + `-SessionCommand ReturnLargeSet`) |

- Useful detail sits in the JSON **`AuditData`** column: Excel → Transform Data → AuditData → Transform → JSON → expand → Close & Load.
- Cross-workload finding pattern: filter each export on the resource property, then **join on user + resource + time window**.
- PowerShell exports: pipe to `Export-Csv` (`-Append` to add record types); use `-Encoding UTF8` to keep non-ASCII characters readable.
- ⚠️ Exam tip: 200,000 Premium records → a single portal export is fine; a single PowerShell call is not.

## Configure audit retention with Audit (Premium)

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-audit-search-investigate/audit-premium-retention

- Retention policies are **Premium only**; Standard keeps the fixed 180-day default.
- Scope by service, specific activities, all/selected users; durations **7 days to 10 years** (10 years needs the add-on license).
- Default Premium policy: 1 year for Entra ID, Exchange, OneDrive, SharePoint; **can't be modified**.
- Role required: **Organization Configuration** (Purview portal).
- Max **50 policies** per organization.
- **Lower priority number wins**; custom policies override the default for the records they cover.
- Retention is set when a record is created: policy/license changes affect **new records only**.
- Non-user activity is fixed at 1 year; policies don't apply.
- Portal: Solutions → Audit → **Audit retention policies**; PowerShell (Security & Compliance): `New-/Get-/Set-/Remove-UnifiedAuditLogRetentionPolicy`.
- Deleting a policy takes up to 30 min; retained records fall back to default retention on next evaluation.

```powershell
New-UnifiedAuditLogRetentionPolicy -Name "Teams Audit Policy" -RecordTypes MicrosoftTeams -RetentionDuration TenYears -Priority 100
```

- ⚠️ Exam tip: custom policy priority 5 (3 years) vs default priority 10 (1 year) → 3 years.

## Where audit fits (from Summary)

| Tool | Role alongside Audit |
|---|---|
| eDiscovery | Preserves content, legal holds, reviews actual message/file/prompt content |
| DLP + Purview Browser Extension | Visibility into non-Microsoft AI apps (feeds `AIAppInteraction`) |
| DSPM | Registers connected AI apps (feeds `ConnectedAIAppInteraction`) |
| Insider Risk Management | Turns behavioral signals into risk scores |
| Defender / Sentinel | Real-time detection, alerting, response; Audit reconstructs events afterwards |

| To investigate | Filter on |
|---|---|
| Deleted email | `ExchangeItem`: `HardDelete`, `SoftDelete`, `MoveToDeletedItems` |
| External file sharing | `SharePointSharingOperation`: `SharingSet`, `AnonymousLinkCreated` |
| File access/download | `SharePointFileOperation`: `FileAccessed`, `FileDownloaded` |
| Messages opened (Premium) | `MailItemsAccessed` |
| Admin/permission changes | `AzureActiveDirectory` + service-specific admin record types |

## Key terms

| Term | Meaning |
|---|---|
| Unified audit log | Single log that all Microsoft 365 workloads write activity records to |
| Record type | Category of an audited activity (e.g. `SharePointFileOperation`) |
| Operation | Specific audited action (e.g. `FileAccessed`) |
| Audit (Standard) | Default tier: search, 180-day retention |
| Audit (Premium) | Adds retention policies, 1-year default for key workloads, high-value events, more API bandwidth |
| Microsoft 365 Advanced Auditing | Per-user service plan that turns on Premium events |
| `MailItemsAccessed` | Premium event showing mail access, as sync or bind |
| `Search-UnifiedAuditLog` | Exchange Online PowerShell cmdlet to search the audit log |
| `AuditData` | JSON column holding the detailed properties of each record |
| Administrative unit | Scope that limits which users' audit logs an admin can search |
| DSPM | Data Security Posture Management; onboards AI apps for audit coverage |

## Exam traps

- Enabling ≠ backfill: tenant auditing and per-user Advanced Auditing only log from enablement forward.
- Audit Reader vs Audit Manager: both search/export; only Manager changes settings. Retention policies need **Organization Configuration**.
- Sync vs bind: sync = bulk download event; bind = individual message opened. Throttling hits **bind** only.
- Retention priority: **lower number wins**, not higher, and not "longest wins".
- Copilot record types: first-party → `CopilotInteraction`; Copilot Studio / Entra-registered → `ConnectedAIAppInteraction`; third-party web AI → `AIAppInteraction`.
- Audit vs eDiscovery: Audit shows an interaction happened; eDiscovery holds and shows content.
- Turning auditing off: PowerShell only.
- Portal export (50k / 1M rows) vs PowerShell call (5,000 max, 100 default).

## Top 3 takeaways

1. One unified audit log covers Microsoft 365 and AI activity; Standard = 180 days, Premium = 1-year default for key workloads + policies up to 10 years.
2. Premium high-value events (`MailItemsAccessed`, `Send`, `SearchQueryInitiated*`) need per-user Advanced Auditing and only exist from enablement forward.
3. Most empty searches are setup issues: auditing state, retention, latency, role/admin-unit scope, UTC, or missing DSPM/DLP dependencies for AI records.
