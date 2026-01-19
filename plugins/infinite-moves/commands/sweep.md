---
name: moves-sweep
description: Scan for technical debt
---

# /moves sweep

Scan codebase for technical debt.

## Options

```
/moves sweep                        # Full scan
/moves sweep --focus <area>         # frontend|backend|types|testing|security
/moves sweep --severity <level>     # Minimum: critical|high|medium|low
/moves sweep --scan-only            # Report only, no manifest update
```

## Categories

| Category | What's Scanned |
|----------|----------------|
| Complexity | Functions >50 lines, deep nesting, large files |
| TODOs | TODO, FIXME, HACK, XXX comments |
| Types | `any` usage, `@ts-ignore`, missing types |
| Dependencies | Outdated, unused, security vulnerabilities |
| Error Handling | Empty catch, unhandled promises |
| Testing | Missing test files, coverage gaps |
| Security | Hardcoded secrets, injection vectors |

## Severity Levels

| Level | Description | Action |
|-------|-------------|--------|
| critical | Security, data loss | Fix immediately |
| high | Maintainability | Fix this sprint |
| medium | Code quality | Fix when touching |
| low | Style, minor | Backlog |

## Output

Creates/updates:
- `.infinite-moves/debt-manifest.json` - Structured items
- `.infinite-moves/debt-report.md` - Human readable

Debt item:
```json
{
  "id": "DEBT-001",
  "category": "security",
  "severity": "critical",
  "title": "Hardcoded API key",
  "file": "src/config.ts",
  "line": 45,
  "suggestedFix": "Use environment variable",
  "effort": "low"
}
```

## Integration with /moves run

```
/moves run --continuous --debt-interval 5 --debt-fix high
```

Every 5 cycles:
1. Pause execution
2. Run sweep
3. Auto-fix trivial/low effort items at high+ severity
4. Resume
