# Module: Experience Security Copilot through guided simulations

> Learning path: [Mitigate threats using Microsoft Security Copilot](https://learn.microsoft.com/en-us/training/paths/sc-200-mitigate-threats-using-microsoft-copilot-for-security/) | Source: [Microsoft Learn module](https://learn.microsoft.com/en-us/training/modules/security-copilot-interactive-guides/)
> Original time: ~110 min (10 units, excl. assessment) | Read time: ~3 min | Last verified: 2026-09-27

## TL;DR

- Hands-on module built entirely from interactive guides (click-through simulations); no new theory beyond the earlier Security Copilot modules.
- Covers the **standalone** experience (owner settings, prompts, promptbooks, custom promptbooks, agents) and the **embedded** experiences in **Microsoft Purview**, **Microsoft Defender XDR**, and **Microsoft Entra** (Conditional Access Optimization Agent).
- Exam relevance: SC-200 now tests investigating incidents with agentic AI and embedded Security Copilot, plus Purview-driven investigations.
- Key rule to remember: Copilot roles and plugins never grant extra access to security data; your Entra and Azure RBAC roles do.

## Exam objectives covered

| Exam domain | Objective (paraphrased) |
|---|---|
| Respond to security incidents | Investigate incidents using agentic AI, incl. embedded Security Copilot |
| Respond to security incidents | Investigate and remediate threats or compromised entities identified by Microsoft Purview (DLP, insider risk) |
| Respond to security incidents | Investigate threats with Content search in Microsoft Purview eDiscovery (Copilot-assisted analysis of results) |

## Explore owner settings in Security Copilot

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-interactive-guides/2-explore-owner-settings-security-copilot

- Interactive guide (~10 min): walks the **Owner** menu in the standalone experience.
- Owner settings control access, integrations, and collaboration for the team's Security Copilot environment.
- Areas shown: workspace management, access controls, integrations.
- ⚠️ Exam tip: owner actions are **scoped to a single workspace**; confirm you are in the right workspace before changing settings.

## Use prompts and promptbooks in Security Copilot

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-interactive-guides/3-use-prompts-promptbooks-security-copilot

| Tool | What it is |
|---|---|
| Prompt | Natural language question or instruction that asks Copilot to perform a task |
| Promptbook | Curated, reusable set of prompts for a common scenario; each prompt builds on the previous one to automate a workflow |
| Plugin | Extends Copilot by integrating with Microsoft and non-Microsoft security solutions |

- Interactions support three areas: threat investigation, troubleshooting and remediation, security posture management.
- A **Copilot contributor** creates sessions and runs prompts and promptbooks.
- Guide tasks: use the prompt bar and manage sources; run prompts and review responses; run a promptbook and review or share its results.
- ⚠️ Exam tip: the Copilot contributor role and enabled plugins do **not** grant permission to view security data; **Microsoft Entra and Azure RBAC roles** decide what data you can see.

## Create a custom promptbook in Security Copilot

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-interactive-guides/4-create-custom-promptbook-security-copilot

- Purpose: turn prompts you keep repeating into a repeatable workflow.
- A custom promptbook is built by **reusing prompts from a previous session**.
- Guide scenario (~10 min): analyst reviewing failed sign-ins and authentication activity in Microsoft Entra; select prompts from the old session, organize them, save as a promptbook.

## Investigate data protection activity in Microsoft Purview

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-interactive-guides/5-investigate-data-protection-activity-purview

| Guide | What it shows |
|---|---|
| Sensitive activity in **Activity explorer** (~10 min) | Review activity signals on sensitive info across SharePoint, OneDrive, and Exchange; filter results; Copilot summarizes the activity to surface the most relevant events |
| **DLP alert** investigation (~5 min) | Alert on a file with sensitive financial data that was accessed and modified; Copilot surfaces alert context and file activity details |

- ⚠️ Exam tip: Activity explorer = broad visibility into sensitive-data activity; a DLP alert = a specific policy match to triage.

## Investigate insider risks and compliance in Microsoft Purview

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-interactive-guides/6-investigate-insider-risks-compliance-purview

| Guide | What it shows |
|---|---|
| **Insider risk alert** (~5 min) | Alert for copying sensitive data to USB; Copilot summarizes user activity, flags key risk patterns, assesses impact across services |
| **Unlabeled sensitive content in eDiscovery** (~5 min) | Copilot analyzes content search results for documents without sensitivity labels; summarizes findings, identifies sensitive information types, shows distribution across workloads |

- Insider Risk Management detects and investigates potentially risky user activity inside the organization.
- Unlabeled-content searches are typical before cloud migrations or regulatory audits, where results can run into thousands of documents.

## Investigate security incidents in Microsoft Defender XDR

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-interactive-guides/7-investigate-security-incidents-defender-xdr

| Guide | What it shows |
|---|---|
| Incident context and activity (~10 min) | Complex incident (compromised asset, dozens of alerts); Copilot gives incident and alert summaries, connects related events and entities, offers guided responses |
| Analyze artifacts and pivot to advanced investigation (~10 min) | Per-alert analysis: alert details, device and user context, identified risks with recommended actions |

- Workflow order: understand overall incident context first, then drill into individual alerts and artifacts.
- ⚠️ Exam tip: embedded Copilot in Defender XDR is used to find where an attack started and how it progressed across many correlated alerts.

## Explore and create an agent in Security Copilot

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-interactive-guides/8-explore-create-agent-security-copilot

- **Agents** automate security tasks by integrating with Microsoft Security solutions and partner services.
- Run modes: **automatically via configured triggers** or **on demand** with selected parameters.
- Agents exist in both the standalone and embedded experiences.
- Guide tasks: review the **Threat Intelligence Briefing Agent** (details, activity, reports) and run it on demand to produce a briefing; create a new agent in the standalone experience.

## Explore the Conditional Access Optimization Agent

> Unit: https://learn.microsoft.com/en-us/training/modules/security-copilot-interactive-guides/9-explore-conditional-access-agent

- Security Copilot agent that analyzes **Conditional Access policies** and recommends improvements.
- Main use: find coverage gaps when **new users or applications** are added that existing policies don't cover.
- Accessed from the **Microsoft Entra admin center**; guide reviews its configuration, activity feed, and suggestions.
- ⚠️ Exam tip: this is an embedded agent in **Entra**, not in the Defender portal.

## Key terms

| Term | Meaning |
|---|---|
| Standalone experience | Security Copilot used directly (sessions, prompts, promptbooks, agents), not inside another product |
| Embedded experience | Security Copilot used inside a product such as Purview, Defender XDR, or Entra |
| Workspace | Scope that Security Copilot owner settings apply to |
| Copilot owner | Role that configures access, integrations, and collaboration settings |
| Copilot contributor | Role that creates sessions and runs prompts and promptbooks |
| Prompt | Natural language input asking Copilot to perform a task |
| Promptbook | Reusable, chained set of prompts for a common scenario |
| Custom promptbook | Promptbook you build from prompts in a previous session |
| Plugin | Integration that extends Copilot to Microsoft and non-Microsoft solutions |
| Agent | Automated Copilot task runner, triggered or on demand |
| Threat Intelligence Briefing Agent | Agent that generates threat intelligence briefings |
| Conditional Access Optimization Agent | Entra agent that finds Conditional Access gaps and suggests fixes |
| Activity explorer | Purview view of activity involving sensitive information |
| Insider Risk Management | Purview solution for detecting and investigating risky internal user activity |

## Exam traps

- Copilot role vs data access: Copilot owner/contributor and plugins give **no** extra data visibility; **Entra and Azure RBAC** do.
- Prompt vs promptbook: a prompt is one instruction; a promptbook chains prompts, each building on the last.
- Promptbook vs custom promptbook: custom ones are created from prompts in an **earlier session**.
- Agent run modes: **trigger-based** (automatic) or **on demand**; not only one or the other.
- DLP alert vs insider risk alert: DLP flags a **policy match on content**; insider risk looks at a **user's risky behavior pattern** (e.g. copying to USB).
- Conditional Access Optimization Agent lives in the **Entra admin center**, not Defender XDR or Purview.

## Top 3 takeaways

1. Security Copilot runs standalone (owner settings, prompts, promptbooks, agents) and embedded in Purview, Defender XDR, and Entra.
2. Copilot roles and plugins don't widen data access; Entra and Azure RBAC roles control what Copilot can show you.
3. Agents automate tasks via triggers or on-demand runs; examples are the Threat Intelligence Briefing Agent and the Conditional Access Optimization Agent.
