# Infinite Moves

A collection of Claude Code skills for AI-assisted development workflows.

## Installation

Install skills using [add-skill](https://github.com/vercel-labs/add-skill):

```bash
npx add-skill <skill-name>
```

## Available Skills

### task-automation

Automated task execution system with manifest-driven workflows, parallel execution, and integrated debt management.

```bash
npx add-skill task-automation
```

**Features:**
- Execute tasks from a JSON manifest
- Run multiple tasks in parallel with conflict detection
- Automatic build verification
- Debt sweep integration for code quality
- Ideation loop for generating new tasks
- Task lifecycle management (claim, verify, complete)

**Commands included:**
| Command | Description |
|---------|-------------|
| `/dev-loop` | Execute tasks from manifest |
| `/task-status` | View pipeline status |
| `/task-claim` | Manually claim/release tasks |
| `/task-verify` | Verify task completion |
| `/ideation-loop` | Generate new tasks from docs or topics |
| `/debt-sweep` | Scan for technical debt |
| `/docs-sync` | Sync documentation with code |
| `/task-loop` | Continuous task execution |

**Quick start:**
```bash
# Install the skill
npx add-skill task-automation

# Create your first task manifest
mkdir -p docs/planning/tasks
echo '{"tasks": []}' > docs/planning/tasks/manifest.json

# Start running tasks
/dev-loop
```

## Skill Structure

Each skill follows the [add-skill](https://github.com/vercel-labs/add-skill) convention:

```
skills/
└── <skill-name>/
    ├── SKILL.md          # Main skill definition (required)
    ├── commands/         # Slash commands
    ├── agents/           # Sub-agent definitions
    └── docs/             # Supporting documentation
```

## Contributing

1. Fork this repository
2. Create your skill in `skills/<skill-name>/`
3. Add a `SKILL.md` with proper YAML frontmatter:
   ```yaml
   ---
   name: your-skill-name
   description: "Brief description of what the skill does"
   ---
   ```
4. Submit a pull request

## License

MIT
