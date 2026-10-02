# Pi Minimal memory

A local-memory Pi package with global, project, and date-based daily scopes. Its initial agent context includes global memory (when present), the saved project/topic inventory, and guidance to preserve durable project updates at session end. It also registers the `memory_list_projects`, `memory_search`, `memory_read`, and `memory_remember` tools, and the `/memory-refine` command.

Agents are told to keep global entries short (a line or two per fact) and to put longer material in project or daily memory. `/memory-refine` is the memory compactor you can run yourself: the user triggers it instead of waiting for a memory to exceed its limit.

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

Memory data is stored locally under `~/.pi/memory/`; this repository contains extension code only. This path is fixed by the extension and does not follow a custom Pi agent-directory setting. The default character limits are 16,000 for global, 8,000 for project, and 16,000 for daily. To override them, create `~/.pi/memory/config.json` with a `limits` object, for example:

```json
{
  "limits": {
    "global": 16000,
    "project": 8000,
    "daily": 16000
  }
}
```

Project and topic names use lowercase letters, digits, and hyphens. When a memory exceeds its configured limit, the extension may compact it using the active authenticated model.

## Tools

- `memory_list_projects` — list saved project topics and global daily dates.
- `memory_search` — literal, case-insensitive search across saved memories.
- `memory_read` — read global, project/topic, or daily memory.
- `memory_remember` — add, edit, or forget a memory entry.

## Commands

- `/memory-refine [global | project <slug> [topic] | daily [YYYY-MM-DD]]` — run the memory compactor on saved memory with the active authenticated model. Without arguments it refines global memory. `project` without a slug uses the working directory name, and without a topic it refines the project's index and every topic; `daily` without a date refines today's file.

This is the same compactor that runs automatically when a memory exceeds its limit, called by hand instead: it prunes stale, duplicated, or low-value entries, merges related facts, tightens wording, and keeps facts that are still useful. The only difference is the size budget — automatic compaction aims below the configured limit, `/memory-refine` also runs on memories that are within it. Files under 200 characters are skipped. In interactive mode the extension asks for confirmation and then reports progress per file; in non-interactive modes it rewrites without asking. Before each rewrite, the current content is copied to `<file>.refine-backup` next to the original (for example `~/.pi/memory/MEMORY.md.refine-backup`), and a file that changed while the model was working is left untouched. Backups are not listed or searched by the tools. If no file is left over its limit afterwards, its pending compaction is cleared; otherwise normal compaction resumes.

Review the source before installing. Extensions execute in the Pi process with that process's operating-system permissions.
