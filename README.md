# it-agent

AI agent skills and tools for managing, maintaining, and updating operating systems.

The project gives an agent a clear place to keep administration workflows (skills) separate from executable operations (tools), with operating-system differences isolated where they actually matter.

## Objectives

- Describe system administration work as reusable skills.
- Run operating-system actions through small, explicit tools when a skill needs them.
- Support the same kinds of tasks on macOS, Linux, and Windows.
- Keep read-only inspection as the normal path, and require approval before changing a machine.

Skill definitions cover system information, operating system updates, software installation and management, routine application updates, and security assessment. See [skills](.agents/skills/README.md). Executable tools, maintenance automation, and troubleshooting workflows are not implemented yet.

## Supported operating systems

- macOS
- Linux
- Windows

## Architecture

Three concepts stay separate:

| Concept | Role |
| --- | --- |
| Skills | Instructions and workflows that say what the agent should do. A skill does not need its own program. |
| Tools | Executable operations that interact with the operating system. Add a tool only when instructions alone are not enough. |
| OS-specific implementations | The parts that must differ between macOS, Linux, and Windows. |

Prefer one shared skill or tool, and branch for the host only where behavior differs. Do not copy a whole workflow per platform when the steps are the same.

## Project layout

- `.agents/skills/<skill-name>/SKILL.md` — one directory per skill. See [skills](.agents/skills/README.md).
- [AGENTS.md](AGENTS.md) — binding instructions for agents working in this repository.
- `data/` — local content files. Contents are gitignored; `.gitkeep` keeps the folder.
- `.gitignore` — files Git must not track, including `data/` contents and secrets.

Tool and per-OS directories are not created until there is something to put in them. There is no `docs/` tree yet.

## Development principles

Binding principles and safety rules are in [AGENTS.md](AGENTS.md): KISS, LEAN, YAGNI, and DRY.

The repository does not yet choose a programming language, framework, or package manager. Pick one when the first tool needs it.

## Safety and permissions

System administration can change or damage the host. [AGENTS.md](AGENTS.md) is the binding policy. In short: read-only by default, explicit approval before a change, consequences stated before that approval, no silent privilege elevation, and no secrets in the repository. These rules are documentation, not an enforcement layer.

## Documentation

- [Agent instructions](AGENTS.md)
- [Skills](.agents/skills/README.md)

## Status

Foundation only.

- Repository license: [The Unlicense](LICENSE) (public domain).
- Five skills are defined. They are instructions only.
- Tools are not implemented.
- No system administration commands are part of this repository yet.

## Possible later work

- Name the read-only native commands for system-information. Still no custom tool.
- Shared tools with a small OS-specific branch once a skill cannot be done with instructions alone.
