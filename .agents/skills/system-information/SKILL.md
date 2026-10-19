---
name: system-information
description: >-
  Collect and explain information about the host: operating system and version,
  hardware and resources, disk usage, and installed applications. Use when the
  user asks what this machine is, for system details, disk space, installed
  software versions, or troubleshooting context. Read-only. Does not change
  configuration or collect secrets.
---

# system-information

## Purpose

Collect and explain information about the host the user named.

Use this skill for operating system identity, hardware and resources, storage, installed applications and versions, and other facts needed to troubleshoot. Prefer the narrowest check that answers the request.

Do not use this skill to change configuration, install or remove software, or apply updates.

## Platforms

macOS, Linux, and Windows. Keep one workflow. Choose the host's native read-only facility only where the mechanism differs.

This repository has no tools. Do not invent one. A later task may add commands; until then, select a native command for the detected host, say whether it is read-only, and follow Safety before running it.

## Steps

1. Confirm which host the user means. Do not inspect an unstated machine.
2. Identify the operating system and version.
3. Report hardware and system resources that the request needs (architecture, CPU, memory).
4. Check disk usage and available storage when storage is relevant.
5. Identify installed applications and versions when the request needs them. Skip a full inventory for a narrow question.
6. Add further troubleshooting context only when it bears on the request. Do not collect personal files, credentials, tokens, or other secrets.

## Report

Print the [report format](../README.md#report-format). Mode is `read-only`.

Body is a short list. One line per fact the request needs: the operating system, a hardware or disk figure, or an application. Include the value and the source. Leave the lines unnumbered.

## Safety

Follow [AGENTS.md](../../../AGENTS.md) and [Skills safety](../README.md#safety).

This skill is read-only. Do not modify system configuration. Do not store secrets in the repository or in `data/`.
