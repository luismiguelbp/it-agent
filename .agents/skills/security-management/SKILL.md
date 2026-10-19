---
name: security-management
description: >-
  Assess the security posture of the operating system and installed
  applications, then recommend remediation. Use when the user asks for a
  security review, to check the OS or applications for security issues, or to
  review firewall settings, missing security updates, or risky configuration.
  Coordinates system-updates, update-applications, and software-management
  instead of repeating them. Read-only by default. Not
  penetration testing or exploit execution.
---

# security-management

## Purpose

Assess and improve the security posture of the operating system and installed applications on the host the user named.

Use this skill to review security settings, identify missing security updates, note outdated or possibly vulnerable applications when the information is reliable, and recommend what to do next.

This skill identifies issues and coordinates remediation. It does not own the operations:

- Operating system updates: [system-updates](../system-updates/SKILL.md).
- Routine application updates: [update-applications](../update-applications/SKILL.md).
- Install or removal: [software-management](../software-management/SKILL.md).

Do not copy those workflows. Hand off the action after the user approves it.

## Platforms

macOS, Linux, and Windows. Review the settings and update channels that exist on that host.

This repository has no tools and no vulnerability database. Do not invent either. Do not claim a vulnerability was detected unless a real tool and a cited data source produced that result.

## Steps

1. Prefer a read-only assessment. State which host and which areas will be checked.
2. Review the OS security settings the request covers, including the firewall and other security configuration when they are in scope.
3. Identify missing operating-system security updates by a check through system-updates. Do not download or install them here.
4. Identify outdated applications through update-applications in check mode. Do not enter its update mode here.
5. Treat an application as potentially vulnerable only when a reliable source says so. Separate that from "update available."
6. Note risky configuration as confirmed findings or as potential risks. Do not merge the two.
7. Rank findings and recommend remediation. For each item, name the other skill that would carry it out.
8. Changing security settings, permissions, software, or other security-sensitive state requires explicit approval and then the skill that owns that change.

Do not perform penetration testing, exploit execution, or other offensive security work.

## Report

Print the [report format](../README.md#report-format). Mode is `read-only` unless the user approved a later change through another skill.

| Index | Finding | Status | Evidence | Skill |
| --- | --- | --- | --- | --- |
| 1 | One finding | `confirmed` or `potential` | The evidence | The skill that would fix it |

Number a finding when the user can choose it for remediation. Put unchecked areas under Omitted. Put recommendations under Actions, separate from anything that actually ran. A short table does not mean the host is secure.

## Safety

Follow [AGENTS.md](../../../AGENTS.md) and [Skills safety](../README.md#safety).

Assessment is read-only. Explicit approval is required before changing security settings, installing or removing software, modifying permissions, or any other security-sensitive operation. Never elevate privileges silently. Never store secrets.
