# Module: Search for content with Microsoft Purview eDiscovery

> Learning path: [Mitigate threats using Microsoft Purview](https://learn.microsoft.com/en-us/training/paths/sc-200-mitigate-threats-using-microsoft-purview/) | Source: [Microsoft Learn module](https://learn.microsoft.com/en-us/training/modules/purview-ediscovery-search/)
> Original time: ~29 min (7 units, excl. assessment) | Read time: ~4 min | Last verified: 2026-09-27

## TL;DR

- eDiscovery in the Microsoft Purview portal lets authorized users create **cases**, search content across Microsoft 365, place holds, and export results.
- Used for internal investigations, legal/regulatory requests, data subject requests, and incident response (e.g. data leak scoping).
- Access requires the **eDiscovery Manager** or **eDiscovery Administrator** role group, plus **case membership**; Global/Compliance Admins get no automatic access.
- Search workflow: define criteria → pick data sources → build the query (conditions, KeyQL, Copilot, or search by file) → run and validate with **Statistics** or **Sample** → export or add to a review set.

## Exam objectives covered

| Exam domain | Objective (paraphrased) |
|---|---|
| Respond to security incidents | Investigate Microsoft 365 activities: perform content search with Microsoft Purview eDiscovery |
| Respond to security incidents | Investigate threats / compromised entities identified by Purview (supporting: scoping leaked or misused content) |

## Understand eDiscovery and content search capabilities

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-ediscovery-search/understand-ediscovery

- eDiscovery = Purview portal feature to create cases, search content, preserve data with holds, and export results.
- The UI is just labeled "eDiscovery"; available features depend on licensing (review sets, custodian management are advanced features).
- **Licensing:** core eDiscovery is in **Microsoft 365 E3 and E5**; advanced features may need separate licensing.
- Work happens in **eDiscovery > Cases**.

| Scenario | Example |
|---|---|
| Internal investigation | HR or security incident: messages, documents, files |
| Legal / regulatory request | Litigation, regulator review |
| Data subject request (DSR) | Find and collect a person's personal data |
| Incident response | Review activity/communications after a breach or data misuse |

- ⚠️ Exam tip: before any search, confirm **both** the role assignment and the license.

## Prerequisites for using eDiscovery in Microsoft Purview

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-ediscovery-search/ediscovery-prerequisites

| Role group | Can do |
|---|---|
| eDiscovery Manager | Create/manage cases, run searches, export results |
| eDiscovery Administrator | Everything eDiscovery Manager can, **plus** manage role assignments and settings across **all cases** |

- **Global Administrator and Compliance Administrator can't access cases or search user content** unless explicitly added to an eDiscovery role group (intentional, auditable access).
- Assign in the Purview portal: **Settings > Roles and Scopes > Role groups** → pick the eDiscovery role group → add users/groups.
- Users may need to sign out and back in before eDiscovery appears.
- Verify: Purview portal → **eDiscovery** → the **Cases** page loads; if not, check role and license.
- ⚠️ Exam tip: "Global Admin can't see the case" → the fix is adding them to **eDiscovery Manager/Administrator** (and to the case), not granting a bigger Entra role.

## Create an eDiscovery search

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-ediscovery-search/create-ediscovery-search

- **Every search belongs to a case**; the case is the workspace for searches, holds, and exports.
- Cases give controlled access, an auditable trail of search/export actions, and a consistent structure.
- The case creator is added as a member automatically; everyone else must be added manually.
- **Role alone isn't enough:** without case membership, a user with an eDiscovery role still can't open the case.

| Method | How |
|---|---|
| Case + search in one step | **Solutions > eDiscovery > Cases** → arrow next to **+ Create case** → **Create search** → case name + search name |
| Case first, then search | **+ Create case** → name → **Create** → case **Searches** tab → **Create search** |

- ⚠️ Exam tip: creating a search directly **also creates a case**; there is no case-less search.

## Conduct an eDiscovery search

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-ediscovery-search/conduct-ediscovery-search

**Searchable workloads**

| Workload | Content |
|---|---|
| Exchange Online | Email, calendar items |
| SharePoint Online / OneDrive for Business | Files, document metadata |
| Microsoft Teams | 1:1 and group chats, channel messages, files shared in chats/channels |
| Microsoft 365 Groups / Viva Engage | Group conversations, shared documents |

**Phases** (iterative: go back and refine as needed)

1. Define search criteria: name, data sources, query.
2. Identify data sources.
3. Build the query.
4. Run and review results.

**Data sources**

- Add users, groups, SharePoint sites, OneDrive accounts, Teams via **Add sources**, or **Add tenant-wide sources** for the whole org.
- Filter the picker: all people and groups (default), people only, groups only.
- For SharePoint/OneDrive, enter the **full site or account URL**.
- Optional: **Exclude inactive users**.
- Narrow scope = more relevant results and better performance.

**Query options**

| Option | What it does |
|---|---|
| Condition builder | Filters: **KeyQL**, **Date** (sent/received/modified), **Subject/Title**, **Participants**, **Type** (Email, Chat, Teams…) |
| KeyQL | Advanced queries in Keyword Query Language |
| Copilot (preview) | Security Copilot turns a natural-language prompt into a suggested KeyQL query you review/edit |
| Search by file (preview) | Upload evidence instead of writing a query: `.txt` = find similar content; `.csv` (e.g. audit log export) = find reference content tied to users/actions |

- Condition operators include **equals**, **contains**, **starts with**.
- Multiple conditions are joined with **AND** (item must match all).
- Condition builder + keyword query together → **both** are applied.
- Search by file: max **10 MB** per file, `.txt` or `.csv` only; KeyQL and condition builder are **disabled** in this mode.

```text
kind:email subject:"budget Q1" AND from:"Sara Davis"
```

- KeyQL basics (from [Purview docs](https://learn.microsoft.com/en-us/purview/edisc-search-query)): `property:value` with no space after the colon; **AND / OR / NOT / NEAR must be uppercase**; a plain space between terms means **OR**; searches are case-insensitive.

**Run and review**

| Result type | Shows |
|---|---|
| Statistics | Item count, result size, breakdown by location |
| Sample | Random sample of items to validate the query before acting |

- Results appear on the **Query** and **Statistics** tabs; adjust conditions/sources and rerun if off-target.
- Next step after validation: **export** or **add to a review set**.
- ⚠️ Exam tip: "KQL" in eDiscovery means **Keyword Query Language (KeyQL)**, not Kusto. Don't bring `| where` syntax here.

## Export eDiscovery search results

> Unit: https://learn.microsoft.com/en-us/training/modules/purview-ediscovery-search/export-results

- Export = downloadable package of items + metadata for legal review, documentation, or handoff.
- Export directly when ready to hand off/archive; add to a **review set** first if deeper analysis is needed.
- Start: case **Searches** tab → select completed search → **Export** → name it and pick item scope.

| Item scope | Use when |
|---|---|
| Only indexed items | Standard export (most investigations) |
| Indexed + partially indexed | Worried about items that couldn't be fully processed |
| Only partially indexed | Special cases, e.g. following up on indexing issues |

| Content type | Export options |
|---|---|
| OneDrive / SharePoint | Versions (latest, recent 10, recent 100, all); include non-matching subfolder items; include SharePoint list attachments |
| Mailboxes / Teams | Thread conversations into **HTML transcripts**; include contextual Teams/Viva Engage messages (**up to 12 hours**); include cloud attachments + version range |
| Format / structure | Mailbox content as **PST** or **MSG**; organize by source location; keep or condense folder path; generate friendly names |

- Export runs in the background; track status, item/location counts, and options used in **Process manager**.
- Download from the case **Exports** tab → export **Overview** → **Export packages** → select files → **Download** (includes content, reports, metadata).
- ⚠️ Exam tip: **Process manager** = monitor; **Exports** tab = download.

## Key terms

| Term | Meaning |
|---|---|
| eDiscovery (Purview) | Case-based search, hold, and export of Microsoft 365 content |
| Case | Workspace containing searches, holds, and exports; access limited to members |
| eDiscovery Manager | Role group: create/manage cases, search, export |
| eDiscovery Administrator | eDiscovery Manager + manage role assignments/settings across all cases |
| KeyQL | Keyword Query Language, the eDiscovery query syntax (`property:value`) |
| Condition builder | UI for building filters (date, participants, type, etc.) combined with AND |
| Search by file (preview) | Search using an uploaded `.txt` sample or `.csv` reference file |
| Statistics / Sample | Result views: summary numbers vs random item sample |
| Partially indexed item | Item that couldn't be fully processed for search indexing |
| Review set | Location for deeper analysis of collected results before export |
| Process manager | View that tracks background eDiscovery jobs such as exports |
| DSR | Data subject request (personal-data request) |

## Exam traps

- Role vs case membership: eDiscovery role grants the capability; **case membership** grants access to a specific case. You need both.
- Global/Compliance Admin vs eDiscovery roles: admin roles give **no** automatic case/content access.
- eDiscovery Manager vs Administrator: only **Administrator** manages role assignments/settings across **all** cases.
- KeyQL vs Kusto KQL: eDiscovery uses **Keyword** Query Language; Advanced Hunting/Sentinel use **Kusto**.
- Statistics vs Sample: Statistics = counts/size/locations; Sample = random items to eyeball relevance.
- Export vs review set: export for handoff/archive; review set for further analysis first.
- Search by file: disables KeyQL and condition builder; `.txt` = similar content, `.csv` = reference content; 10 MB max.

## Top 3 takeaways

1. eDiscovery access needs an **eDiscovery Manager/Administrator** role group **and** case membership; Global Admin alone isn't enough.
2. Every search lives in a case; build queries with the condition builder (AND), KeyQL, Copilot, or search by file, then validate with Statistics or Sample.
3. Export offers indexed/partially indexed scope, SharePoint version options, Teams threading (12 h context), and PST/MSG output; track in Process manager, download from the Exports tab.
