# Module: Describe the core features of Microsoft Security Copilot

> Learning path: [Mitigate threats using Microsoft Security Copilot](https://learn.microsoft.com/en-us/training/paths/sc-200-mitigate-threats-using-microsoft-copilot-for-security/) | Source: [Microsoft Learn module](https://learn.microsoft.com/en-us/training/modules/security-copilot-describe-core-features/)
> Original time: ~58 min (8 units, excl. assessment) | Read time: ~6 min | Last verified: 2026-09-27

## TL;DR

- The **standalone experience** is Security Copilot's own portal: agents, promptbooks, session history, owner settings, usage monitoring, and the prompt bar.
- **Sessions** are conversations made of prompts; each prompt shows a process log, and results can be pinned, shared, and exported.
- **Workspaces** split one tenant into separate environments, each with its own capacity (SCUs), settings, plugins, and permissions.
- **Plugins** (Microsoft, Other, Websites, Custom) connect Copilot to data sources; **promptbooks** chain prompts; **knowledge bases** (file upload or Azure AI Search) add org context.

## Exam objectives covered

| Exam domain | Objective (paraphrased) |
|---|---|
| Respond to security incidents | Investigate incidents using agentic AI, incl. Security Copilot (standalone features, Defender XDR plugin, promptbooks) |
| Respond to security incidents | Investigate Sentinel alerts and incidents (supporting: Sentinel plugin) |
| Perform threat hunting | Identify threats with KQL (supporting: natural language to KQL plugins) |

## Describe the features available in the standalone experience of Microsoft Security Copilot

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-describe-core-features/2-describe-standalone-experience

- Landing page landmarks: **navigation panel, workspaces, prompts to try, prompt bar**. Access depends on Copilot role permissions.

| Navigation item | What it does |
|---|---|
| Agents | Microsoft and non-Microsoft AI agents; depending on role, set them up or run them |
| Promptbooks | Library of built-in and custom promptbooks (description, prompt count, owner) |
| Build (preview) | Build, test, and publish custom agents (from scratch or YAML manifest) |
| History | Past sessions of the user |
| Owner settings | Switch SCU capacity, choose workspace for agents, data sharing ("Improve Copilot"), Purview audit logging, who can upload files |
| Plugin settings | Who can add custom plugins (for self / for org): *Contributors and owners* or *Owners only*; restrict plugins to owners; allow access to Microsoft 365 data |
| Role assignments | Manage **Copilot Owner** and **Copilot Contributor** assignments; add recommended roles group |
| Manage workspaces | Create and configure workspaces |
| Usage monitoring | SCU consumption dashboard per workspace: units used, plugins, session initiators; filters and export |
| Security Store | Storefront to find, try, buy, and deploy Microsoft and partner security solutions and agents |

- Ellipsis menu (bottom left): **Settings** (theme, language, time zone; data and privacy; app version), **Help**, **Tenant switcher**.
- Session retention: kept until **manually deleted**; on delete, data gets a **30-day TTL**. Logs with session data are unaffected and kept **up to 90 days**.
- Usage dashboard holds **up to 90 days** of data.
- Capacity: **provisioned SCUs** (predictable workloads) + **overage SCUs** (on demand, billed only when used).
- At **90%** of provisioned capacity, analysts get a warning (also in embedded experiences); above the limit, prompts are blocked until the owner adds SCUs.
- Support cases need at least **Service Support Administrator** or **Helpdesk Administrator**; self-help is open to all Copilot users.
- Tenant switcher: the Copilot tenant need not be the analyst's sign-in tenant; users can work across multiple tenants.
- Prompts to try: filter prompts/promptbooks by **role** (e.g. CISO, SOC analyst) or **plugin** (e.g. Defender XDR, NL to KQL).
- Prompt bar icons: **run**, **prompt icon** (promptbooks + system capabilities), **sources icon** (Manage sources: Plugins tab by default, Files tab).
- **System capabilities** (prompt suggestions) = single prompts; the list depends on enabled plugins, though some are plugin-independent.
- ⚠️ Exam tip: session deletion (30-day TTL) does **not** delete logs (up to 90 days).

## Describe the features available in a session of the standalone experience

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-describe-core-features/2a-describe-session-features

- **Session** = one conversation of one or more prompts; started from the prompt bar, a promptbook, or a prompt suggestion.
- **Process log**: shows which capability produced the response (to judge source trust) and that **safety checks** ran (responsible AI).
- Per prompt/response actions: pin, edit, rerun, delete, export prompt, copy response, give feedback.
- Feedback options: **Looks right / Needs improvement / Inappropriate** (also in embedded experiences).
- **Pin board**: pin one, several, or all prompt-response pairs; opens in split view with a **Summary** tab and a **Pinned items** tab; session title editable.
- Pin board output: **share** with org users who have Copilot access, **export to Word**, **email**, or **copy**.
- ⚠️ Exam tip: the process log is how an analyst verifies *which capability/source* generated an answer.

## Describe workspaces

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-describe-core-features/2b-describe-workspaces

- **Workspace** = separate work environment inside the tenant (tenant = house, workspace = room).
- Benefits: per-team setup, cost mapping per team, avoid throttling of critical workflows, geo-specific session storage, per-team plugins/promptbooks/files, safe testing of custom plugins and promptbooks, segmentation of agentic traffic.

| Action | Requirement |
|---|---|
| Create a workspace | At least **Security Administrator** |
| Attach capacity (SCUs) | **Azure subscription Owner or Contributor** |
| Manage / delete workspaces | Workspace **owners** (Manage workspaces page) |

- Create: name, new or existing capacity, **data storage location**, data sharing preferences; can differ from the first-run setup.
- Plugin settings, role permissions, and owner settings apply **per workspace**.
- Exception: **audit logging** can only be changed by **Security Admins** and applies to **all workspaces**.
- Users pick a workspace from the landing page and can set a **preferred workspace**.
- After switching the preferred workspace, users must reload or sign out/in to Defender, Intune, Entra, or Purview portals, or Copilot errors there.
- ⚠️ Exam tip: everything is workspace-scoped except audit logging, which is tenant-wide.

## Describe Security Copilot plugins

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-describe-core-features/3-describe-microsoft-plugins

| Category | Source | Authentication |
|---|---|---|
| Microsoft | Microsoft security products (many preinstalled) | Mostly **on-behalf-of (OBO)**: uses the user's licenses and sign-in; some need setup parameters |
| Other | Non-Microsoft services (e.g. ServiceNow, Splunk, CrowdSec, GreyNoise) | Service-specific (API keys, OAuth, etc.); needs account and license |
| Websites | Public web industry info | **Anonymous**, no configuration |
| Custom | Built by you/your org | Defined by the plugin; needs a **YAML or JSON manifest** |

- Plugged-in products must be **purchased separately**.
- Default: all Owners and Contributors can use preinstalled plugins. Owners can restrict availability to **All users** or **Owners only**.
- After restricting, **new preinstalled plugins default to Owners only**; the change is immediate and also hits embedded experiences. Restricted plugins show greyed out.
- Plugins with a gear icon or **Set up** button (e.g. **Sentinel, Azure AI Search**) are configured **per user**.
- Each enabled plugin exposes **system capabilities** (see all via prompt icon → *See all system capabilities*).
- Custom plugin types: **Copilot plugins** and plugins built with the **OpenAI API**. Owner settings control who can add them.

| Plugin pair | Main plugin | NL-to-KQL plugin | Extra permissions |
|---|---|---|---|
| Defender XDR | Summarize incidents, guided responses, incident reports, device summaries, file/script analysis | Natural-language hunting question → ready-to-run KQL | **None** beyond Copilot access |
| Sentinel | Incidents, related alerts, workspace data | Natural-language hunting question → ready-to-run KQL | A Sentinel role (e.g. **Microsoft Sentinel Reader**) + configure **workspace, subscription, resource group** |

- Both products have a built-in **incident investigation promptbook** (incident report with alerts, reputation scores, users, devices).
- ⚠️ Exam tip: Defender XDR plugin needs no extra roles; Sentinel plugin needs a Sentinel role and per-user setup.

## Describe custom promptbooks

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-describe-core-features/5-describe-custom-promptbooks

- **Promptbook** = series of prompts run in sequence, each building on the previous responses.
- Create from an existing session: select prompts → **Create promptbook** → set name, tag, description; add, edit, or remove prompts.
- Input parameters: short name in **angle brackets, no spaces** (e.g. `<IncidentID>`, `<ThreatActor>`); multiple allowed.
- Share with the organization or keep personal; share via link after creation.
- Run from the prompt icon (search by name) or the promptbook library (**Get started**), entering parameters first.
- Library actions: view details, **edit (owner of the promptbook only)**, duplicate, delete.
- **Continue on failure**: keeps running later prompts when one fails.
- ⚠️ Exam tip: only the promptbook's owner can edit it; others can duplicate it instead.

## Describe knowledge base connections

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-describe-core-features/5a-describe-knowledge-base-connections

- Knowledge base connections (**preview**) add org content (wikis, policies, procedures, templates, KQL libraries) for more tailored answers.

| | File upload | Azure AI Search plugin |
|---|---|---|
| Where | Sources icon → Manage sources → **Files** | Plugin connected to an Azure AI Search index |
| Limits | DOCX, MD, PDF, TXT; **3 MB per file, 20 MB total** | **One index at a time** (change plugin settings to switch) |
| How to invoke | Mention **"uploaded files"** (or the file name); toggle file on per session | Mention **"Azure AI Search"** in the prompt |
| Visibility | **Only the uploading user** | Configured per user (plugin setup) |
| Notes | Stored in tenant's **home Geo**, in the Security Copilot service (**outside** the tenant boundary); not for malware detonation | Credentials **not validated on save**; errors appear at run time; semantic + keyword search |

- File upload control (owners): **No one** or **Contributors and Owners**; default is Contributors and Owners.
- Azure AI Search index requirements: text field **searchable**, title field **filterable**, vector field uses **text-embedding-ada-002** (integrated vectorization helps).
- Shared limits: text only; structured content such as tables is not well supported. AI Search shows source titles without hyperlinks.
- ⚠️ Exam tip: uploaded files are private to the uploader, even inside the same tenant.

## Key terms

| Term | Meaning |
|---|---|
| Standalone experience | Security Copilot's dedicated portal |
| Embedded experience | Copilot used inside other security portals (Defender, Intune, Entra, Purview) |
| SCU | Security Compute Unit: capacity unit that powers Copilot workloads |
| Provisioned vs overage SCUs | Fixed capacity vs on-demand capacity billed only when used |
| Session | One Copilot conversation made of one or more prompts |
| Process log | Per-prompt trace of the capability used and safety checks |
| Pin board | Saved prompt-response pairs with a session summary; shareable and exportable |
| Workspace | Separate Copilot environment within a tenant with its own capacity and settings |
| Plugin | Connector that extends Copilot with a data source or service |
| System capability | Single built-in prompt exposed by a plugin (prompt suggestion) |
| Promptbook | Ordered set of prompts run as one workflow |
| OBO | On-behalf-of authentication: Copilot acts with the user's own access |
| NL2KQL | Natural language to KQL plugins for Defender XDR and Sentinel |
| Knowledge base connection | Org content added via file upload or Azure AI Search (preview) |

## Exam traps

- Copilot Owner vs Contributor: owners control plugins, file upload, capacity, and workspaces; contributors use Copilot.
- Workspace-scoped vs tenant-wide: plugin, role, and owner settings are per workspace; **audit logging** is tenant-wide and Security Admin only.
- Create workspace vs attach capacity: Security Administrator vs Azure subscription Owner/Contributor.
- Defender XDR vs Sentinel plugin: no extra role vs Sentinel role + workspace/subscription/resource group setup.
- File upload vs Azure AI Search: private files up to 3 MB each / 20 MB total vs one connected index; each needs its keyword in the prompt.
- Session deletion vs logs: 30-day TTL on session data; logs kept up to 90 days.
- System capability vs promptbook: one prompt vs a chained sequence.

## Top 3 takeaways

1. The standalone portal centralizes prompts, promptbooks, agents, history, and owner controls; sessions are traceable (process log) and shareable (pin board).
2. Workspaces segment one tenant by team, capacity (SCUs), and settings; only audit logging is tenant-wide.
3. Plugins (OBO for Microsoft, service auth for others), custom promptbooks, and knowledge bases (file upload, Azure AI Search) extend what Copilot can reason over.
