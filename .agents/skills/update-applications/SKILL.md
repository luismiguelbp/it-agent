---
name: update-applications
description: >-
  Routine application update workflow with check mode and update mode. Use
  check mode when the user asks to check for application updates or show
  outdated software. Use update mode when the user asks to update applications
  or perform routine application maintenance. Check mode only reports. Update
  mode runs one package-manager command for every package that manager
  already covers, then reports the other apps. Does not update the operating
  system (use system-updates). Installing an app the manager does not already
  handle belongs to software-management.
---

# update-applications

## Purpose

Provide the convenient path for routine application maintenance.

Use this skill when the user asks to update applications, check for application updates, show outdated software, or perform routine application maintenance.

Operating system updates belong to [system-updates](../system-updates/SKILL.md). Installing, uninstalling, and package or dependency changes belong to [software-management](../software-management/SKILL.md). Call that skill for those operations. Do not copy its steps or invent a second implementation.

## Modes

Pick the mode from the request. Stay in check mode unless the user asked to apply updates.

| Mode | When | Result |
| --- | --- | --- |
| Check | "Check for application updates." "Show outdated software." A check delegated by security-management. | Report available updates. Do not download or install. |
| Update | "Update my applications." Routine maintenance where the user asked to apply updates. | After approval, run the one bulk command. Then analyze apps that command does not cover. |

The request selects the mode. Approval runs the bulk command. It is not approval to change apps outside that package manager.

## Platforms

macOS, Linux, and Windows. Use a package manager only when it is already installed. Do not install one to answer the question, and do not assume it covers every application.

This repository has no tools. Do not invent one.

Do this in two phases. First the package manager, with one command for every package it already manages. Then the apps that command does not cover.

| Platform | Bulk command | List, for check mode | What the command skips |
| --- | --- | --- | --- |
| macOS, when `brew` is installed | `brew update`, then `brew upgrade`. `brew update` only fetches formulae. `brew upgrade` upgrades the outdated packages Homebrew already manages. | `brew outdated --verbose`, without letting Homebrew auto-run `brew update` | Pinned packages. Casks that update themselves, unless the user also approves `--greedy`. |
| Linux, when `apt` is installed | `apt update`, then `apt upgrade`. `apt update` only refreshes indexes. `apt upgrade` installs the pending package upgrades. | `apt list --upgradable` | Packages `apt upgrade` holds back. Another manager, such as `dnf` or `pacman`, when that is the one already in use. Do not also run `apt`. |
| Windows, when `winget` is installed | `winget source update`, then `winget upgrade --all`. `winget update` is an alias of `winget upgrade`. | `winget upgrade` with no package and without `--all` | Packages with an unknown version or a pin, unless the user approves the matching include flag. |

Check mode runs the list command only. Update mode runs the bulk command only after the user approves that one command. Do not ask for one index per package inside the bulk set.

## Steps

1. Confirm the host and that the request is application maintenance, not an OS update.
2. Select check mode or update mode.
3. Discover the package manager the host already uses. If none is installed, skip the bulk phase.
4. List what the bulk command would change. Keep that check read-only.
5. Print the [report format](../README.md#report-format) before applying anything. The body starts with the bulk command. Numbered rows are only apps the bulk command will not update and that have their own updater.
6. Under Omitted, name apps the bulk command skips and apps with no updater. Do not switch an app onto a package manager that does not already manage it.
7. Check mode stops after the report.
8. Update mode: state the effect, the host, and whether the bulk command is reversible. After one approval, run that command. Then analyze the remaining apps. Do not update those apps in the same approval.
9. Do not change application settings or remove user data. Installing or removing an app still belongs to software-management.

## Report

Print the [report format](../README.md#report-format). Mode is `check` or `update`.

Body starts with the bulk command: the command, the package count, notable version jumps, and what it skips. Do not number packages inside that set.

| Index | App | Installed | Available | Updater | Risk |
| --- | --- | --- | --- | --- | --- |
| 1 | One app the bulk command will not update | Installed version | Available version | Its own updater | Restart or data risk |

Check mode stops after that report. Update mode runs the bulk command only after one approval, then repeats the report for whatever that command left.

## Safety

Follow [AGENTS.md](../../../AGENTS.md) and [Skills safety](../README.md#safety).

Check mode is read-only. The bulk command modifies the host and needs its own approval. Apps outside that command need a later approval. Never elevate privileges silently. `apt upgrade` and `winget upgrade --all` may ask for privileges. Say so before running them.
