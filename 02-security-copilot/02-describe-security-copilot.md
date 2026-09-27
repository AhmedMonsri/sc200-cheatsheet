# Module: Describe Microsoft Security Copilot

> Learning path: [Mitigate threats using Microsoft Security Copilot](https://learn.microsoft.com/en-us/training/paths/sc-200-mitigate-threats-using-microsoft-copilot-for-security/) | Source: [Microsoft Learn module](https://learn.microsoft.com/en-us/training/modules/security-copilot-getting-started/)
> Original time: ~22 min (7 units, excl. assessment) | Read time: ~5 min | Last verified: 2026-09-27

## TL;DR

- Security Copilot is a cloud-based, generative-AI security analysis tool that helps analysts triage, investigate, and respond at machine speed, offsetting attack growth and the security talent shortage.
- It runs as a **standalone** portal (prompt bar) and **embedded** inside products such as Defender XDR; both consume the same capacity (SCUs).
- An **orchestrator** plans each prompt, picks **capabilities** from enabled **plugins**, grounds the answer in data, runs responsible-AI checks, and exposes a **process log**.
- Onboarding: identify license category (M365 E5/E7 = auto-provisioned; others = manual SCUs) → provision capacity → set up environment → assign roles, all mostly **per workspace**.
- Copilot never exceeds the user's own access: Copilot roles grant platform access only; data access still needs product roles.

## Exam objectives covered

| Exam domain | Objective (paraphrased) |
|---|---|
| Respond to security incidents | Investigate incidents with agentic AI, incl. embedded Security Copilot (foundations: terminology, agents, plugins, access model) |
| Perform threat hunting | Create Advanced Hunting queries (concept only: Copilot generates KQL from natural language) |

## Get acquainted with Microsoft Security Copilot

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-getting-started/2-describe-security-copilot

**Headline use cases**

| Use case | What Copilot does |
|---|---|
| Investigate & remediate threats | Summarizes incidents, gives step-by-step response guidance |
| Build KQL / analyze scripts | Natural language → KQL; explains suspicious scripts without manual reverse engineering |
| Risk & posture | Prioritized view of environment risks |
| IT troubleshooting | Synthesizes information into actionable fixes |
| Security policies | Drafts policies, checks conflicts, summarizes existing ones |
| Secure lifecycle workflows | Guided group creation and access configuration |
| Stakeholder reports | Audience-tailored summaries of context, open issues, protections |

| Experience | Where | Example |
|---|---|---|
| Standalone | Dedicated Security Copilot site, prompt bar; output as text, images, or documents | Free-form investigation sessions |
| Embedded | Inside a product's UI | Defender XDR: incident summaries, script analysis, KQL generation in advanced hunting |

- Built on **Azure OpenAI Service** (LLMs for NLP) plus **security-specific sources**: Microsoft global threat intelligence (65+ trillion daily signals), plugins, and knowledge-base connections.
- Plugins connect Microsoft products, non-Microsoft products, and open-source intelligence feeds; knowledge bases add organization-specific context.
- Customer data stays with the organization and is **not used to train foundation models**.
- ⚠️ Exam tip: standalone vs embedded is a key distinction; the embedded Defender XDR experience is what the "embedded Security Copilot" exam objective refers to.

## Describe Microsoft Security Copilot terminology

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-getting-started/3-describe-terminology

| Term | Definition |
|---|---|
| Session | One conversation; Copilot keeps context within it |
| Prompt | A single question/instruction entered in the prompt bar |
| Capability (a.k.a. skill) | A function that solves part of a problem, scoped to its data source |
| Plugin | A collection of capabilities for a particular resource |
| Workspace | Separate Copilot work environment within the tenant |
| Agent | AI tool that autonomously handles high-volume security/IT tasks |
| Orchestrator | Backend that composes capabilities to answer a prompt |

- **Promptbooks** and prompt suggestions give preselected prompts for newer users (detail in a later module).
- Defender XDR plugin capabilities: incident summary, guided response (recommended actions), script/code analysis, NL → KQL, incident reports; a Sentinel plugin offers similar capabilities scoped to Sentinel only.
- Plugins exist for Microsoft services, non-Microsoft services (e.g. ServiceNow, Splunk), websites, and custom plugins.
- Some plugins need setup (Setup button / gear icon): Microsoft plugins for resource details, non-Microsoft plugins usually for authentication.
- **Workspaces**: tenant = house, workspace = room; users switch tenant, then work in workspaces they can access, within their role in each.
- Each workspace needs **its own capacity**; benefits include per-team cost mapping and geo-specific session data storage.
- **Agents**: tailored to scenarios (threat protection, identity, data security), integrate with Microsoft and partner tools, learn from feedback, operate within Zero Trust.
- ⚠️ Exam tip: capability = skill; plugin = container of capabilities; orchestrator = the planner that chooses among them.

## Describe how Microsoft Security Copilot processes prompt requests

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-getting-started/4-describe-how-copilot-processes-prompts

| Step | What happens |
|---|---|
| 1. Submit prompt | User enters prompt in the prompt bar |
| 2. Orchestrator | Determines initial context and builds a plan from available capabilities |
| 3. Build context | Executes the plan to gather the data needed |
| 4. Plugins | Reasons over all enabled plugins and data sources |
| 5. Responding | LLM composes a human-readable answer from data + context |
| 6. Response | Formatted and reviewed against responsible-AI checks |
| 7. Receive response | Delivered to the user |

- The **process log** shows which capability was used (e.g. Incident Analysis) and that safety checks ran.
- Purpose of the process log: lets the user judge whether the answer came from a trusted source.
- ⚠️ Exam tip: the orchestrator plans before data is fetched; responsible-AI review happens before the response is returned.

## Describe the elements of an effective prompt

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-getting-started/5-create-effective-prompts

| Element | Meaning | Example |
|---|---|---|
| Goal | The specific security information you need | Info on a named threat actor |
| Context | Why you need it / how it's used | Time frame; for a manager report |
| Expectations | Output format or audience | Table, action list, summary, diagram |
| Source | Known data, sources, or plugins to use | "in Microsoft Defender XDR" |

- Be specific, clear, concise; start simple and add detail as you gain experience.
- Iterate: follow-up prompts refine results; like any LLM, the same prompt can yield slightly different answers.
- Add context to narrow where Copilot looks (e.g. name the product holding the incident).
- Give **positive instructions** (what to do, incl. how to handle exceptions) rather than "don't do X".
- Address Copilot directly as "You" ("You should…").
- ⚠️ Exam tip: memorize the four elements — **Goal, Context, Expectations, Source**.

## Describe how to enable Microsoft Security Copilot

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-getting-started/6-describe-how-to-enable-security-copilot

**Onboarding order:** identify customer category → provision capacity (if required) → set up default environment → assign role permissions.

| Customer category | Onboarding |
|---|---|
| Microsoft 365 E5 / E7 | Included; zero-click auto-provisioning, no Azure setup; 7-day advance notice before activation |
| Others | Manual onboarding: provision Security Compute Units (SCUs) |

**Capacity (non-E5/E7)**

- **SCU** = unit of compute for Copilot, used by both standalone and embedded experiences.
- Provisioned SCUs billed **hourly**; overage billed **on usage**, set to a maximum or unlimited; adjust anytime, no long-term commitment.
- Purchase **1–100 SCUs**; recommended for initial exploration: **3 SCUs + unlimited overage**.
- Prerequisites: Azure subscription; **Azure Owner or Contributor** at resource-group level (minimum); **Security Administrator** or higher in the tenant.
- Two paths: Security Copilot first-run wizard (**recommended**, also creates the workspace) or the Azure portal service page.
- Either path creates a resource group in the subscription; SCUs are an Azure resource in it; scale later in Azure portal or Copilot.
- Usage monitoring dashboard (capacity owners, per workspace): units used, plugins used, session initiators, filters, export; **up to 90 days**.

**Default environment setup** (minimum role: Security Administrator)

| Setting | Scope | Notes |
|---|---|---|
| SCU capacity | Per workspace | Each workspace has its own capacity |
| Data storage location | Per workspace | EU (EUDB), UK, US, Australia & NZ, Japan, Canada, South America |
| Prompt evaluation location | ⚠️ Unverified: scope not stated in unit | Within your geo only, or anywhere in the world |
| Audit logging in Microsoft Purview | **Tenant-wide (all workspaces)** | Owner settings; logs admin/user actions and responses; non-Purview tenants need a limited setup |
| Data sharing (2 toggles) | Per workspace | Human review for product performance; human review for Microsoft's security AI model — neither permits foundation-model training |
| Plugin settings | Per workspace | Who can add custom plugins (self vs everyone); restrict plugins to owners; allow access to Microsoft 365 services |

- The Microsoft 365 services toggle is required for the **Microsoft Purview plugin**; changing it needs **Copilot owner** or **Global Administrator**.

**Roles** (assigned per workspace; least privilege)

| Role | Notes |
|---|---|
| Copilot owner | Platform role (not an Entra role); no data access by itself |
| Copilot contributor | Platform role; no data access by itself |

- Entra roles inheriting Copilot owner: Billing Admin, Entra Compliance Admin, Global Admin, Intune Admin, Security Admin.
- Purview roles inheriting Copilot owner: Compliance Admin, Data Governance Admin, Organization Management.
- Only **Global Admin, Security Admin, or Copilot owner** can add/remove Owner and Contributor members.
- **Recommended Microsoft Security roles** group: exists only in Copilot, bundles Entra roles; add it to Contributor for quick access for existing security staff.
- Plugins have their own role needs, e.g. Sentinel plugin → **Microsoft Sentinel Reader**; Intune plugin → **Intune Endpoint Security Manager**.
- Microsoft plugins mostly use **OBO (on behalf of)**: Copilot acts with the signed-in user's licenses and access; some plugins use configured auth parameters instead.
- Plugin enablement and configuration are **per workspace**.
- ⚠️ Exam tip: a Global Administrator isn't automatically Azure Owner/Contributor; Entra roles don't grant Azure resource access until access management is elevated.

## Key terms

| Term | Meaning |
|---|---|
| Security Copilot | Generative-AI security analysis tool, standalone and embedded |
| Standalone experience | Dedicated Security Copilot portal driven by the prompt bar |
| Embedded experience | Copilot features built into product UIs (e.g. Defender XDR) |
| Session | A conversation in which Copilot keeps context |
| Prompt | Natural-language instruction or question in the prompt bar |
| Capability / skill | Function Copilot uses for a specialized task in a data source |
| Plugin | Collection of capabilities for one resource |
| Orchestrator | Copilot backend that plans and composes capabilities |
| Workspace | Separate Copilot environment inside a tenant, with its own capacity |
| Agent | Autonomous AI assistant for high-volume security/IT tasks |
| Promptbook | Predefined series of prompts |
| Process log | Shows capability used and safety checks for a response |
| SCU | Security Compute Unit: Copilot compute capacity |
| Overage | On-demand SCUs billed by usage when provisioned SCUs run out |
| OBO | On behalf of: plugin accesses products with the user's own access |
| Recommended Microsoft Security roles | Copilot-only group bundling Entra security roles |

## Exam traps

- Copilot owner/contributor vs Entra roles: Copilot roles only grant **platform** access, never security data.
- Copilot role vs plugin role: a Contributor still needs e.g. **Sentinel Reader** to use the Sentinel plugin.
- E5/E7 vs other licenses: E5/E7 = automatic zero-click; others = manual SCU provisioning in Azure.
- Provisioned vs overage: provisioned = hourly billing; overage = usage billing, capped or unlimited.
- Global Admin vs Azure roles: Global Admin doesn't include Azure Owner/Contributor by default.
- Tenant vs workspace scope: Purview audit logging is **tenant-wide**; storage, data sharing, plugins, roles, capacity are **per workspace**.
- Data sharing opt-in ≠ foundation-model training.
- Capability vs plugin: capability = single function; plugin = set of capabilities.

## Top 3 takeaways

1. Core vocabulary: session, prompt, capability (skill), plugin, workspace, agent, orchestrator; the orchestrator plans, plugins supply data, responsible-AI checks precede the answer.
2. Effective prompts contain **Goal, Context, Expectations, Source**; iterate and use positive instructions.
3. Onboarding: E5/E7 auto-provisioned vs manual 1–100 SCUs; Copilot owner/contributor give platform access only, with per-workspace settings and OBO plugin access limited to the user's own rights.
