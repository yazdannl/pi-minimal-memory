# Pi Minimal memory

A local-memory Pi package with global, project, and date-based daily scopes. Its initial agent context includes global memory (when present), the saved project/topic inventory, and guidance to preserve durable project updates at session end. It also registers the `memory_list_projects`, `memory_search`, `memory_read`, and `memory_remember` tools.

## Install

Install for your user (the default):

```bash
pi install git:github.com/yazdannl/pi-minimal-memory
```

For one invocation without saving the package in settings:

```bash
pi -e git:github.com/yazdannl/pi-minimal-memory
```

To install in the current project's settings instead, add `-l` to `pi install`; project packages load only after the project is trusted. Use `pi list` to check installed packages, `pi update --extensions` to update them, and `pi remove git:github.com/yazdannl/pi-minimal-memory` to uninstall.

If you already load `memory.ts` directly from `~/.pi/agent/extensions/`, remove that duplicate registration before installing this package.

The package manifest loads `extensions/memory.ts`. Pi provides its imported runtime packages; no separate runtime dependency installation is needed.

## Storage and limits

Memory data is stored locally under `~/.pi/memory/`; this repository contains extension code only. This path is fixed by the extension and does not follow a custom Pi agent-directory setting. The default character limits are 4,000 for each scope. To override them, create `~/.pi/memory/config.json` with a `limits` object, for example:

```json
{
  "limits": {
    "global": 4000,
    "project": 4000,
    "daily": 4000
  }
}
```

Project and topic names use lowercase letters, digits, and hyphens. When a memory exceeds its configured limit, the extension may compact it using the active authenticated model.

## Tools

- `memory_list_projects` — list saved project topics and global daily dates.
- `memory_search` — literal, case-insensitive search across saved memories.
- `memory_read` — read global, project/topic, or daily memory.
- `memory_remember` — add, edit, or forget a memory entry.

Review the source before installing. Extensions execute in the Pi process with that process's operating-system permissions.
