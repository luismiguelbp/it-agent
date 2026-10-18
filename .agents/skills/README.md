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

- A skill states the goal, the steps, and the expected evidence.
- If a step is a native command, write the command, the platform it applies to, and whether it is read-only or mutating.
- If a step needs a tool, name the tool and the inputs it requires. Do not copy the tool's implementation into the skill.
- The skill decides whether the step is allowed to run. The tool performs the operation. Neither should elevate privileges on its own.

## Naming and documentation

- Directory and skill name: lowercase kebab-case (`system-info`, `install-package`).
- The `name` in `SKILL.md` matches the directory name.
- Title the file with the skill name. Open with a short description of when to use it.

Each `SKILL.md` includes:

1. Purpose and when to use the skill.
2. Platforms it covers.
3. Steps, including commands or tools.
4. What the agent should report back.
5. Safety: read-only or mutating, what approval is required, and any destructive or security-sensitive effects.

## Safety

- Default the workflow to read-only inspection.
- Do not tell the agent to run a command merely because it appears in the skill. The user must have asked for that outcome.
- Mutating steps require explicit user approval in the conversation before they run.
- Before asking, state what will change, on which machine, and the consequences, including data loss or downtime when those apply.
- Call out destructive and security-sensitive steps separately from ordinary edits.
- Never include a step that elevates privileges without saying so and waiting for approval.
- Do not put passwords, keys, tokens, or other secrets in skill files. Refer to a local secret store or prompt the user instead.
