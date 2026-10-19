# Skills

Skills are instructions and workflows for the agent. They describe when to act, which checks to make, and when to stop and ask. They are not programs.

A skill may rely only on native operating-system commands and the agent's own judgment. Add or call a tool only when the workflow needs an executable operation that instructions cannot express cleanly.

## Organization

One directory per skill:

```text
.agents/skills/<skill-name>/SKILL.md
```

Put extra notes or examples in that directory only when `SKILL.md` would be unclear without them. Do not add a skill folder until the workflow is real.

Shared workflows live once under `.agents/skills/`. Mention macOS, Linux, and Windows differences inside the skill when the steps diverge. Split or move OS-specific steps into a tool only when the skill would otherwise duplicate large procedures.

Rules for agents working in the repo are in [AGENTS.md](../../AGENTS.md).

## Skills and tools

Skills are instructions and workflows: what to do, which checks to make, and when to stop for approval. Tools are executable operations that perform the work. A skill does not need its own program. No tools exist in this repository yet. Do not claim a tool or capability exists unless it is implemented.

- A skill states the goal, the steps, and the expected evidence.
- If a step is a native command, write the command, the platform it applies to, and whether it is read-only or mutating.
- If a step needs a tool, name the tool and the inputs it requires. Do not copy the tool's implementation into the skill.
- The skill decides whether the step is allowed to run. The tool performs the operation. Neither should elevate privileges on its own.

## Current skills

| Skill | Purpose | Boundary |
| --- | --- | --- |
| [system-information](system-information/SKILL.md) | Collect and explain host facts. | Read-only. No configuration changes. |
| [system-updates](system-updates/SKILL.md) | Inspect and apply operating system updates. | OS updates only. Not application updates. |
| [software-management](software-management/SKILL.md) | Install, uninstall, and inspect packages and dependencies. | Not the routine "update my applications" path. |
| [update-applications](update-applications/SKILL.md) | Routine application updates. One bulk package-manager command, then the apps it does not cover. | Not OS updates. Install and removal go through software-management. |
| [security-management](security-management/SKILL.md) | Assess OS and application security and recommend fixes. | Identifies and coordinates. Does not repeat the other four skills. |

## Choosing a skill

| User request | Skill |
| --- | --- |
| "Show me my system information." OS, hardware, disk space, or installed versions. | system-information |
| "Check for operating system updates." Install OS or OS security updates. Does this OS update need a restart? | system-updates |
| "Install Git." Is this application installed? Uninstall, or inspect a package and its dependencies. | software-management |
| "Check for application updates" or "show outdated software" (check mode). "Update my applications" (update mode). | update-applications |
| "Check my OS and applications for security issues." Firewall, missing security updates, or risky configuration. | security-management |

If one request needs two skills, start with the row above and hand off the other part. security-management may find missing OS updates, outdated applications, or software that should be installed or removed. The matching skill carries out that change after approval.

## Naming and documentation

- Directory and skill name: lowercase kebab-case (`system-info`, `install-package`).
- The `name` in `SKILL.md` matches the directory name.
- Title the file with the skill name. Open with a short description of when to use it.

Each `SKILL.md` includes:

1. Purpose and when to use the skill.
2. Platforms it covers.
3. Steps, including commands or tools.
4. What the agent should report back: the [report envelope](#report-format), and the body for this skill.
5. Safety: read-only or mutating, what approval is required, and any destructive or security-sensitive effects.

## Report format

Every skill prints this envelope. That skill defines the body.

```text
Skill: <skill name>
Host: <machine>
Mode: <read-only, check, update, or the stage that ran>
Result: <completed, stopped for approval, failed, or incomplete>

<skill body>

Omitted: items with no row, and why. Leave this off when nothing was omitted.
Actions: what ran, and what is only a recommendation. Before asking for approval, name the effect, the host, and whether it is reversible.
Errors: failures and checks that did not finish. Leave this off when there are none.
```

Number a row only when the user can approve or select it, starting at 1 for this report. An empty body is valid; say why there are no rows.

## Safety

- Default the workflow to read-only inspection.
- Do not tell the agent to run a command merely because it appears in the skill. The user must have asked for that outcome.
- Mutating steps require explicit user approval in the conversation before they run.
- Before asking, state what will change, on which machine, and the consequences, including data loss or downtime when those apply.
- Call out destructive and security-sensitive steps separately from ordinary edits.
- Never include a step that elevates privileges without saying so and waiting for approval.
- Do not put passwords, keys, tokens, or other secrets in skill files. Refer to a local secret store or prompt the user instead.
- Do not treat a command as safe only because it is a standard administration command.
- Do not claim a tool or capability exists unless this repository implements it.
- Distinguish a recommendation from an action that was executed.
- Report errors and incomplete work as incomplete.
