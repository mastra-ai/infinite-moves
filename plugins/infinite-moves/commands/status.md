---
name: moves-status
description: View pipeline health and manage tasks
---

# /moves status

View and manage the task pipeline.

## Options

```
/moves status                       # Summary view
/moves status --all                 # All tasks with details
/moves status --available           # Available tasks only
/moves status --velocity            # Completion trends
/moves status --next <n>            # Top N recommendations
/moves status --claim <id>          # Claim for manual work
/moves status --release <id>        # Release claimed task
/moves status --complete <id>       # Mark task done
/moves status --verify <id>         # Verify completion
```

## Summary View

Default output:
```
TASK STATUS
═══════════════════════════════════════

Progress: 119/142 tasks (84%)
████████████████░░░░ 84%

Velocity: 1.7 tasks/day ↑

By Priority:
  P0: 2/2 complete
  P1: 9 available
  P2: 13 available

Top 3 Available:
  • [P0] G56-healthkit-integration
  • [P1] G58-nutrition-backend
  • [P1] G50-onboarding-ui
```

## Velocity Analysis

With `--velocity`:
- Tasks completed last 7 days
- Daily average
- Trend vs prior week
- Time by complexity (low/medium/high)
- Projected completion

## Task Management

**Claim** (`--claim <id>`):
```
✓ Claimed: G17-health-sync
Task file: .infinite-moves/tasks/G17-health-sync.md
When done: /moves status --complete G17-health-sync
```

**Complete** (`--complete <id>`):
- Update status to "completed"
- Add completedAt timestamp
- Show newly unblocked tasks

**Verify** (`--verify <id>`):
- Check expected files exist
- Run build
- Check acceptance criteria
- Report: VERIFIED | FAILED | MANUAL_REVIEW

## Symbols

- `✓` completed
- `◉` in progress
- `○` available
- `◌` blocked
- `↑` improving
- `↓` declining
