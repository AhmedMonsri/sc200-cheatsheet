# SC-200 Cheat Sheet — Microsoft Security Operations Analyst

Condensed, exam-focused study notes for **Exam SC-200: Microsoft Security Operations Analyst**, covering every learning path, module, and unit of the official Microsoft Learn course (SC-200T00-A).

The goal: turn hours of Microsoft Learn content into short, reliable revision notes — roughly **30 minutes of reading per learning path**.

> ⚠️ These notes are a revision aid, not a replacement for the official content or hands-on labs. Always verify against the linked Microsoft Learn sources — products and features change often.

## How to use this repo

1. Start with [00-exam-overview](./00-exam-overview/) to see the exam domains and how they map to modules.
2. Work through each learning path folder in order. Each module file links back to its official source.
3. Use [kql-quick-reference.md](./kql-quick-reference.md) and [exam-traps.md](./exam-traps.md) for last-minute revision.

## Exam domains

Per the [official study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/sc-200) (skills measured as of October 21, 2026):

| Domain | Weight |
|---|---|
| Manage a security operations environment | 40–45% |
| Respond to security incidents | 35–40% |
| Perform threat hunting | 20–25% |

## Learning paths

| # | Learning path | Original time | Read time | Status |
|---|---|---|---|---|
| 01 | [Mitigate threats using Microsoft Defender XDR](./01-defender-xdr/) | TBD | TBD | ⬜ Not started |
| 02 | [Mitigate threats using Microsoft Security Copilot](./02-security-copilot/) | TBD | TBD | ⬜ Not started |
| 03 | [Mitigate threats using Microsoft Purview](./03-purview/) | TBD | TBD | ⬜ Not started |
| 04 | [Mitigate threats using Microsoft Defender for Endpoint](./04-defender-for-endpoint/) | TBD | TBD | ⬜ Not started |
| 05 | [Mitigate threats using Microsoft Defender for Cloud](./05-defender-for-cloud/) | TBD | TBD | ⬜ Not started |
| 06 | [Create queries for Microsoft Sentinel using KQL](./06-sentinel-kql/) | TBD | TBD | ⬜ Not started |
| 07 | [Configure your Microsoft Sentinel environment](./07-sentinel-environment/) | TBD | TBD | ⬜ Not started |
| 08 | [Connect logs to Microsoft Sentinel](./08-sentinel-connect-logs/) | TBD | TBD | ⬜ Not started |
| 09 | [Create detections and perform investigations using Microsoft Sentinel](./09-sentinel-detections-investigations/) | TBD | TBD | ⬜ Not started |
| 10 | [Perform threat hunting in Microsoft Sentinel](./10-sentinel-threat-hunting/) | TBD | TBD | ⬜ Not started |

Status legend: ⬜ Not started · 🟨 In progress · ✅ Done

## Repo structure

```
sc200-cheatsheet/
├── 00-exam-overview/        # exam objectives map + glossary
├── 01-… to 10-…             # one folder per learning path, one file per module
├── templates/               # module file template
├── prompts/                 # prompt used to generate module files
├── kql-quick-reference.md
└── exam-traps.md
```

## Sources

All content is written in my own words from official Microsoft sources only:

- [Course SC-200T00-A on Microsoft Learn](https://learn.microsoft.com/en-us/training/courses/sc-200t00)
- [SC-200 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/sc-200)
- [Microsoft security documentation](https://learn.microsoft.com/en-us/security/)

## Contributing

Corrections are welcome — see [CONTRIBUTING.md](./CONTRIBUTING.md).

## Disclaimer

This is an independent community project, not affiliated with or endorsed by Microsoft. Microsoft, Microsoft Defender, Microsoft Sentinel, Microsoft Purview, and Microsoft Security Copilot are trademarks of the Microsoft group of companies.

_Last updated: 2026-09-27_
