---
name: software-management
description: >-
  Manage the software lifecycle: whether an application is installed, its
  version, install and uninstall, package managers, and dependencies. Use when
  the user asks to install, remove, or inspect software packages. Routine
  application update requests belong to update-applications. Operating system
  updates belong to system-updates.
---

# software-management

## Purpose

Manage the software lifecycle on the host the user named: presence, version, install, uninstall, package managers, and dependencies.

Use this skill when the user asks to install, remove, or inspect specific software or packages. Also use it when [update-applications](../update-applications/SKILL.md) needs an install or package operation. Do not reimplement that operation inside the other skill.

Routine requests such as "update my applications" or "show outdated software" belong to [update-applications](../update-applications/SKILL.md). Operating system updates belong to [system-updates](../system-updates/SKILL.md).

The routine bulk upgrade stays in [update-applications](../update-applications/SKILL.md). Use this skill when that bulk command cannot update an app, or when the user asks to install, remove, or inspect a package.

## Platforms

macOS, Linux, and Windows. Inspect the mechanisms the host already has. Examples, not requirements: macOS native tools and Homebrew; the Linux distribution's package manager; Windows native tools and WinGet. Do not install a package manager just to answer a question.

This repository has no tools. Do not invent one, and do not duplicate a package operation that another skill already delegates here.

## Steps

1. Identify the operating system and the software mechanisms already available.
2. Check whether the named application is installed and which version.
3. For install or uninstall, inspect the package and its dependencies first.
4. Explain what will change, on which host, and the consequences (removed files, dependent packages, services that stop).
5. Install, uninstall, or update only after explicit approval.
6. When update-applications delegates a package change, perform that change here and return the result. Do not take over the rest of the routine update workflow.

## Report

Print the [report format](../README.md#report-format). Mode is `read-only` for a presence check, or `install` or `uninstall` after approval.

| Index | Package | Installed | Version | Mechanism | Dependencies | Action |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | One package or application | Yes or no | Version, when known | How it is managed | Dependencies that the action would change | Taken, or still proposed |

Number the rows when asking for install or uninstall. A presence check leaves Index off. Ask for approval by index before install or uninstall.

## Safety

Follow [AGENTS.md](../../../AGENTS.md) and [Skills safety](../README.md#safety).

Checking presence and version is read-only. Install and uninstall modify the host and need explicit approval. Do not change application settings or delete user data as a side effect of a package action unless the user approved that effect. Never elevate privileges silently.
