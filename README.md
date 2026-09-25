# Pi Extension: Memory Tools

A local-memory extension for Pi with scoped global, project, and date-based daily memories. It registers `memory_list_projects`, `memory_search`, `memory_read`, and `memory_remember` tools, and adds global memory to the session context when present.

## Install

From this repository's root:

```bash
mkdir -p ~/.pi/agent/extensions
cp memory.ts ~/.pi/agent/extensions/
```

Restart Pi or run `/reload`. To try it for one invocation without copying it:

```bash
pi --extension ./memory.ts
```

The path above is Pi's default user agent directory. If you use a custom agent directory, install the file under its `extensions/` directory instead.

## Storage and limits

Memory data is stored locally under `~/.pi/memory/`; this repository contains extension code only. This storage path is fixed by the extension and does not follow a custom Pi agent-directory setting. The default character limits are 4,000 for each scope. To override them, create `~/.pi/memory/config.json` with a `limits` object, for example:

```json
{
  "limits": {
    "global": 4000,
    "project": 4000,
    "daily": 4000
  }
}
```

Project and topic names use lowercase letters, digits, and hyphens. When a memory exceeds its configured limit, the extension may compact it using the active authenticated model. No separate CLI or npm dependency is required; Pi supplies the imported runtime packages.

## Tools

- `memory_list_projects` — list saved project topics and global daily dates.
- `memory_search` — literal, case-insensitive search across saved memories.
- `memory_read` — read global, project/topic, or daily memory.
- `memory_remember` — add, edit, or forget a memory entry.

Review the source before loading it. Like other Pi extensions, it runs in the Pi process with that process's operating-system permissions.
