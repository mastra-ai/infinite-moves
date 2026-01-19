---
name: moves-ideate
description: Generate tasks from topics, gaps, or code analysis
---

# /moves ideate

Generate new tasks for the manifest.

## Options

```
/moves ideate                           # Full gap analysis
/moves ideate --quick                   # Fast health check (no file changes)
/moves ideate --topic "description"     # Generate from an idea
/moves ideate --from-spec <file>        # Generate from existing doc
/moves ideate --focus <area>            # Focus: frontend|backend|mobile|testing
```

## Topic Mode

When given `--topic "feature idea"`:

1. **Research** - Web search if unfamiliar tech
2. **Design doc** - Create in `.infinite-moves/designs/`
3. **Break down** - Split into 1-4 hour tasks
4. **Task specs** - Create `.infinite-moves/tasks/G{N}-{slug}.md`
5. **Update manifest** - Add with dependencies

Example:
```
/moves ideate --topic "Add push notifications for reminders"

Creates:
- .infinite-moves/designs/push-notifications.md (design)
- .infinite-moves/tasks/G48-push-model.md
- .infinite-moves/tasks/G49-push-api.md
- .infinite-moves/tasks/G50-push-ui.md
```

## Gap Analysis Mode

When run without topic:

1. **Scan docs** - Find documented features
2. **Scan code** - Find implementations
3. **Compare** - Identify gaps:
   - Documented but not built
   - Built but not tested
   - TODOs/FIXMEs in code
   - Missing error handling
4. **Generate tasks** - For each gap found

## Quick Mode

With `--quick`:
- Read manifest, count tasks by status
- Quick grep for TODOs
- Report pipeline health
- NO file changes
- ~10 seconds

Output:
```
Pipeline: 22 available | 1 in_progress | 0 blocked
Status: HEALTHY
Recommendation: No immediate ideation needed
```

## Task Spec Template

```markdown
# G48: Push Notification Model

## Overview
Database model and types for push notifications.

## Acceptance Criteria
- [ ] NotificationToken model in schema
- [ ] Migration created and tested
- [ ] Types exported

## Files to Create/Modify
| File | Action | Description |
|------|--------|-------------|
| src/db/schema/notifications.ts | Create | Token model |
| src/db/migrations/004_notifications.ts | Create | Migration |

## Definition of Done
- [ ] All criteria met
- [ ] Build passes
```

## Manifest Entry

```json
{
  "id": "G48-push-model",
  "title": "Push Notification Model",
  "file": "G48-push-model.md",
  "priority": "P1",
  "complexity": "low",
  "status": "available",
  "dependencies": [],
  "tags": ["backend", "database"],
  "source": "ideation --topic"
}
```
