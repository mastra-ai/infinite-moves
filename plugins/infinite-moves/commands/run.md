---
name: moves-run
description: Execute tasks from the manifest
---

# /moves run

Execute tasks from `.infinite-moves/manifest.json`.

## Options

```
/moves run                          # Single task
/moves run --parallel <n>           # N concurrent tasks
/moves run --continuous             # Until all done
/moves run --infinite               # Never stop - ideate when backlog empties
/moves run --cycles <n>             # Run n cycles then stop
/moves run --debt-interval <n>      # Debt sweep every n cycles
/moves run --debt-fix <level>       # Auto-fix debt (critical|high|medium|low)
/moves run --ideate-threshold <n>   # Ideate when < n tasks remain (default: 3)
```

## Execution Flow

1. Read manifest, find highest priority available task
2. Check dependencies are met
3. Claim task (update status to "claimed")
4. Read task spec from `.infinite-moves/tasks/<file>`
5. Execute: create/modify files per acceptance criteria
6. Verify: run `npm run build` (or project's build command)
7. Update manifest to "completed"
8. Purge completed tasks from manifest

## Infinite Mode

With `--infinite`:
- Runs tasks continuously like `--continuous`
- When available tasks drop below threshold (default 3), triggers `/moves ideate`
- Ideation scans for gaps, generates new tasks
- Resumes execution with fresh backlog
- Never stops unless manually interrupted or ideation finds nothing new

Use `--ideate-threshold N` to control when ideation triggers:
```
/moves run --infinite --ideate-threshold 5   # Ideate when < 5 tasks left
```

**The Loop:**
```
┌─────────────────────────────────────────┐
│  1. Execute highest priority task       │
│  2. Mark complete, purge from manifest  │
│  3. Check: tasks remaining < threshold? │
│     YES → Run /moves ideate             │
│     NO  → Continue                      │
│  4. Repeat forever                      │
└─────────────────────────────────────────┘
```

## Parallel Mode

With `--parallel N`:
- Select N non-conflicting tasks
- Conflict = same domain (both backend) or file overlap
- Safe pair = one backend + one frontend
- Pre-claim all before spawning agents
- Spawn via Task tool with `run_in_background: true`

## Agent Spawning

```javascript
Task({
  description: "G17: Implement Health Sync",
  prompt: `Execute task G17...

  [task spec contents]

  Output when done:
  <!-- AGENT_SUMMARY_START -->
  {"taskId": "G17", "status": "completed", "buildStatus": "passed"}
  <!-- AGENT_SUMMARY_END -->`,
  subagent_type: "general-purpose",
  run_in_background: true
})
```

## Debt Integration

With `--debt-interval N`:
- Every N cycles, pause and run `/moves sweep`
- If `--debt-fix <level>`, auto-fix trivial items at that severity
- Resume task execution after sweep

## Manifest Update

After completion:
```json
{
  "status": "completed",
  "completedAt": "2024-01-19T..."
}
```

Then purge completed tasks to keep manifest lean.
