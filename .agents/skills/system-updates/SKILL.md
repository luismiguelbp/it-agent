---
name: system-updates
description: >-
  Inspect and manage operating system updates only. Use when the user asks to
  check, download, or install OS updates, to find operating-system security
  updates, or to learn whether an OS update requires a restart. Application
  updates belong to update-applications. Separate check, download, install,
  and restart. Do not modify the system or reboot without explicit approval.
---

# system-updates

## Purpose

Inspect and manage operating system updates on the host the user named.

Use this skill for the operating system's own updates, including security updates shipped through that OS update mechanism, and for restart requirements that come from those updates.

Application updates belong to [update-applications](../update-applications/SKILL.md). Installing or removing software belongs to [software-management](../software-management/SKILL.md).

## Platforms

macOS, Linux, and Windows. Identify the OS and its update mechanism. Do not assume one package manager is the OS updater.

This repository has no tools. Do not invent one. Select the host's native update mechanism, label each step read-only or mutating, and follow Safety before running it.

## Steps

1. Identify the operating system, version, and its update mechanism.
2. Check for available OS updates. Keep the check read-only when the mechanism can list updates without downloading or installing them.
3. Summarize pending updates and why they matter. Mark security updates when the mechanism distinguishes them. If it does not, say so.
4. Keep four stages separate: check, download, install, and restart. Do not collapse them into one action.
5. Explain prerequisites, risks, and whether a restart is required. Name the effect, the host, and whether the change is reversible.
6. Stop. Download, install, or restart only after the user explicitly approves that stage.

## Report

Print the [report format](../README.md#report-format). Mode is the stage that ran: `check`, `download`, `install`, or `restart`.

| Index | Update | Version | Security | Restart | Pending |
| --- | --- | --- | --- | --- | --- |
| 1 | One operating system update | Version | Security update, or that the mechanism does not say | Whether a restart is required | Stage still pending |

Put the update mechanism in Actions. Ask for approval by index before download, install, or restart.

## Safety

Follow [AGENTS.md](../../../AGENTS.md) and [Skills safety](../README.md#safety).

Checking is the default. Download, install, and restart modify or interrupt the host. Each needs its own approval after the consequences are stated. Never reboot or elevate privileges without that approval.
