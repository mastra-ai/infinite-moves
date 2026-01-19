# Infinite Moves

Multiple loops for infinite task generation and execution.

## Why "Infinite"?

Most agent loops work like this:

```
Get task → Execute → Verify → Repeat → Stop (backlog empty)
```

They execute a predefined backlog until it's empty. **Infinite Moves is different** - it generates its own work:

```
┌───────────────────────────────────────────────────┐
│                                                   │
│   Ideation ───→ Execution ───→ Debt Sweep        │
│       ↑               │             │             │
│       └───────────────┴─────────────┘             │
│                                                   │
│   The loops feed each other. Work never ends.    │
└───────────────────────────────────────────────────┘
```

| Aspect | Typical Agent Loop | Infinite Moves |
|--------|-------------------|----------------|
| Work source | Predefined backlog | Self-generated via ideation |
| Loops | 1 (execution) | 3 (ideation, execution, debt) |
| Ends when | Backlog empty | Never - ideates more work |
| Maintenance | Manual | Self-maintaining via debt sweep |

**Ideation** scans docs, code, and your ideas to generate tasks. **Execution** works through them. **Debt sweep** keeps quality high. When the backlog runs low, ideation finds more. It's perpetual, self-sustaining development.

## Architecture

Two components working together:

| Component | Purpose |
|-----------|---------|
| **Skill** | Makes Claude aware of the plugin, enables autonomous invocation |
| **Plugin** | Provides the commands and agents |

```
User: "Help me with my backlog"
        ↓
Skill activates (matches "backlog")
        ↓
Claude knows to use /moves commands
        ↓
Invokes /moves status, /moves run automatically
        ↓
Spawns task-executor agents
```

## Commands

| Command | Purpose |
|---------|---------|
| `/moves run` | Execute tasks (single, parallel, continuous) |
| `/moves ideate` | Generate tasks from topics or gap analysis |
| `/moves status` | View pipeline, manage tasks |
| `/moves sweep` | Scan for technical debt |

## Quick Start

```bash
# Initialize the data directory
mkdir -p .infinite-moves/tasks .infinite-moves/designs
echo '{"tasks": []}' > .infinite-moves/manifest.json

# Let Claude work autonomously
"Generate tasks for user authentication and start working on them"

# Or use commands explicitly
/moves ideate --topic "user authentication"
/moves run --continuous --parallel 2
```

## Data Directory

All artifacts are stored in `.infinite-moves/` at the project root:

```
.infinite-moves/
├── manifest.json           # Task manifest
├── tasks/                  # Task spec files
│   └── G17-health-sync.md
├── designs/                # Design docs from ideation
│   └── push-notifications.md
├── debt-manifest.json      # Technical debt tracking
└── debt-report.md          # Human-readable debt report
```

## Structure

```
infinite-moves/
├── skills/infinite-moves/
│   └── SKILL.md                    # Awareness layer
│
└── plugins/infinite-moves/
    ├── plugin.json                 # Plugin manifest
    ├── commands/
    │   ├── run.md                  # /moves run
    │   ├── ideate.md               # /moves ideate
    │   ├── status.md               # /moves status
    │   └── sweep.md                # /moves sweep
    └── agents/
        ├── task-executor.md        # Executes tasks
        └── verifier.md             # Verifies completion
```

## The Loops

**Ideation Loop**
- Generate from topic: design doc → tasks
- Gap analysis: docs vs code → tasks
- Code scan: TODOs, untested code → tasks

**Execution Loop**
- Pick highest priority available
- Execute (or spawn agent)
- Verify build
- Update manifest, purge completed

**Debt Loop**
- Scan for complexity, TODOs, type issues
- Track in debt manifest
- Auto-fix trivial items during continuous runs

## License

MIT
