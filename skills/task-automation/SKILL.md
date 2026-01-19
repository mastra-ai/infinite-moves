---
name: task-automation
description: "Execute development tasks from manifest, run parallel agents, verify builds, manage task pipeline. Use when user wants to run tasks, check task status, execute dev loop, or work through the backlog."
allowed-tools: Read, Edit, Bash, Glob, Grep, Task, TodoWrite
user-invocable: true
---

# Task Automation Skill

Automated task execution system for any project. Manages a task manifest and executes tasks via parallel agents.

## Installation

```bash
npx add-skill task-automation
```

This will install:
- `SKILL.md` - Main skill definition
- `commands/` - Slash commands for task management
- `agents/` - Sub-agent definitions for task execution
- `docs/` - Supporting documentation

## When to Use This Skill

- User says "run tasks", "execute tasks", "dev loop", "work on backlog"
- User asks about task status or pipeline health
- User wants to run multiple tasks in parallel
- User mentions the manifest or available tasks

## Quick Start

1. Create a manifest at `docs/planning/tasks/manifest.json`
2. Add task spec files in `docs/planning/tasks/`
3. Run `/dev-loop` to start executing tasks

## Core Concepts

### Task Manifest
Located at `docs/planning/tasks/manifest.json`. Contains:
- Task definitions with ID, title, priority, complexity
- Status tracking (available, claimed, in_progress, completed)
- Dependencies between tasks
- Tags for categorization (backend, frontend, etc.)

### Task Lifecycle
```
available → claimed → in_progress → completed → (auto-purged)
                  ↓
              blocked/abandoned
```

### Automatic Manifest Cleanup
Completed tasks are automatically purged from the manifest to keep it lean and manageable. This happens:
- After each dev-loop cycle completes
- When updating a task status to "completed"

**Cleanup preserves:**
- Tasks with status: available, claimed, in_progress, blocked, abandoned
- All metadata and schema configuration

**Cleanup removes:**
- Tasks with status: completed

This keeps the manifest focused on actionable work. Completed task specs remain in `docs/planning/tasks/` for historical reference.

### Conflict Detection
Tasks conflict if they:
1. Modify the same files (check task spec's "Files to Create/Modify")
2. Both have `backend` tag (may touch shared entry points)
3. Both have `frontend` tag AND modify shared files (App.tsx, routes)

Safe parallel pairs: one backend + one frontend task.

## Workflows

### Single Task Execution
1. Read manifest, find highest priority available task
2. Claim task (update manifest status)
3. Read task file from `docs/planning/tasks/<file>`
4. Execute task (create/modify files per spec)
5. Run build verification (`npm run build`)
6. Update manifest to completed
7. **Purge completed tasks** from manifest (cleanup step)

### Parallel Execution (--parallel N)
1. **Acquire manifest lock** before reading
2. Read manifest, find N non-conflicting available tasks
3. Pre-claim all tasks in manifest (prevents race conditions)
4. **Release manifest lock** after claiming
5. Spawn N background agents via Task tool
6. Each agent works independently on its task
7. Monitor agent outputs for completion
8. **Acquire lock**, update manifest for completed tasks, **release lock**
9. Verify all builds pass

### Manifest Locking Protocol
To prevent race conditions when multiple agents access the manifest:

```bash
# Acquire lock (waits up to 10s if held by another agent)
npx ts-node scripts/manifest-lock.ts acquire <agent-id>

# Release lock when done
npx ts-node scripts/manifest-lock.ts release <agent-id>

# Check lock status
npx ts-node scripts/manifest-lock.ts status

# Force release stale lock (use with caution)
npx ts-node scripts/manifest-lock.ts force-release
```

**Lock Rules:**
- Always acquire lock before reading/modifying manifest
- Release lock immediately after changes are written
- Locks auto-expire after 60 seconds (stale lock protection)
- Lock file: `docs/planning/tasks/manifest.lock` (gitignored)

### Manifest Validation
Before executing any tasks, validate the manifest structure:

```bash
npx tsx scripts/validate-manifest.ts
```

If validation fails, fix the issues before proceeding. Common validation errors:
- Invalid task ID format (should be `G{number}-{slug}`)
- Missing required fields (id, title, file, priority, status)
- Invalid status/priority values
- Dependencies referencing non-existent tasks
- File name not matching task ID

The manifest schema is defined at `docs/planning/tasks/manifest.schema.json`.

### Pipeline Check
1. Validate manifest (fail fast if invalid)
2. Read manifest, count by status: available, completed, blocked
3. Check if ideation needed (available < threshold)
4. Report pipeline health

### Debt Sweep Integration
Triggered every N cycles (default: 5) during continuous execution:

1. **Check cycle counter** - Track cycles since last debt sweep
2. **Pause execution** when counter hits debt-interval threshold
3. **Run debt sweep** - Scan codebase for new technical debt
4. **Fix eligible items** if `--debt-fix` level is set:
   - Only fix items with effort = trivial or low
   - Fix in severity order: critical → high → medium → low
   - Stop at specified severity level
5. **Update manifests** - `debt-manifest.json` and `debt-report.md`
6. **Report findings** - Show new debt, fixed items, remaining items
7. **Reset counter** and resume task execution

## Agent Output Format

All spawned agents MUST output this structured summary:

```
<!-- AGENT_SUMMARY_START -->
{
  "taskId": "<task-id>",
  "status": "completed|partial|failed|blocked",
  "filesCreated": ["<paths>"],
  "filesModified": ["<paths>"],
  "buildStatus": "passed|failed",
  "acceptanceCriteria": {"total": N, "met": N, "failed": N},
  "errors": [],
  "notes": "<summary>"
}
<!-- AGENT_SUMMARY_END -->
```

## Available Commands

| Command | Description |
|---------|-------------|
| `/dev-loop` | Execute tasks from manifest |
| `/task-status` | View pipeline status |
| `/task-claim` | Manually claim/release tasks |
| `/task-verify` | Verify task completion |
| `/ideation-loop` | Generate new tasks |
| `/debt-sweep` | Scan for technical debt |
| `/docs-sync` | Sync docs with implementation |
| `/task-loop` | Continuous task execution |

## Usage Examples

**Run single task:**
```
/dev-loop
```

**Run 2 tasks in parallel:**
```
/dev-loop --parallel 2
```

**Continuous with debt sweep every 3 cycles:**
```
/dev-loop --continuous --debt-interval 3
```

**Auto-fix high+ severity debt during continuous run:**
```
/dev-loop --continuous --debt-interval 5 --debt-fix high
```

**Focused feature work (skip debt sweeps):**
```
/dev-loop --continuous --no-debt-sweep
```

**Check status:**
```
/task-status
```

**Natural language (auto-detected):**
- "Let's knock out some tasks"
- "Run the dev loop"
- "What tasks are available?"
- "Execute P1 tasks in parallel"
- "Run tasks and clean up debt along the way"
- "Do a debt sweep every 3 tasks"

## File Structure

After installation, your `.claude/` directory will look like:

```
.claude/
├── skills/
│   └── task-automation/
│       ├── SKILL.md
│       ├── commands/
│       │   ├── dev-loop.md
│       │   ├── task-status.md
│       │   ├── task-claim.md
│       │   ├── task-verify.md
│       │   ├── ideation-loop.md
│       │   ├── debt-sweep.md
│       │   ├── docs-sync.md
│       │   └── task-loop.md
│       ├── agents/
│       │   ├── task-executor.md
│       │   └── verifier.md
│       └── docs/
│           ├── parallel-execution.md
│           └── manifest-schema.md
└── commands/ (symlinked from skill)
```

## Project Setup

Your project needs:
1. `docs/planning/tasks/manifest.json` - Task manifest
2. `docs/planning/tasks/*.md` - Task specification files
3. Build command (e.g., `npm run build`)

See `docs/manifest-schema.md` for the manifest format.
