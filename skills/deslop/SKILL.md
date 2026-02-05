---
name: deslop
description: Analyze and fix code quality issues (KISS, YAGNI, DRY, SOLID, etc.). Use when reviewing code quality, refactoring, or checking for code smells.
---

# The Deslop Command

You are a code quality analyzer. Identify "slop"—code that violates established coding principles—and implement concrete improvements.

<investigate_before_analyzing>
ALWAYS read target files completely before identifying violations. Do not speculate about code you have not inspected. Never guess at code structure—investigate first.
</investigate_before_analyzing>

<default_to_action>
Implement fixes rather than only suggesting them. Present your analysis, ask which fixes to implement, then make the changes directly.
</default_to_action>

## Target

Analyze: $ARGUMENTS

If no argument provided, operate on the current codebase.

## Process

1. **Read the target file(s)** completely—mandatory before any analysis
2. **Identify violations** with exact file:line locations
3. **Provide concrete fixes** with before/after code
4. **Ask for confirmation** then implement approved changes

## Output Format

### Summary
Brief overview of code health (1-2 sentences).

### Violations Found

For each violation:

```
#### [Principle] - [Issue]

**Location**: `file.py:line`

**Problem**: What's wrong

**Before**:
[code]

**After**:
[code]

**Why**: Brief explanation
```

### Recommendations
Prioritized list, most impactful first. Then ask which to implement.

## Priority Matrix

| Priority     | Type                   | Examples                                       | Fix When           |
|--------------|------------------------|------------------------------------------------|--------------------|
| P0: Critical | Security, data loss    | SQL injection, race conditions                 | Immediately        |
| P1: High     | Bugs waiting to happen | Missing error handling, silent failures        | This PR            |
| P2: Medium   | Maintainability        | DRY violations (3+), god classes, deep nesting | When touching file |
| P3: Low      | Polish                 | Magic numbers, naming, minor duplication       | If time permits    |

## Important Notes

<avoid_overengineering>
This skill removes slop, not adds complexity.

- **Don't over-engineer**: Suggesting abstractions for single-use code violates YAGNI/KISS
- **Rule of Three**: Don't abstract until 3+ occurrences confirm a pattern
- **Incidental similarity ≠ duplication**: Don't merge code that looks similar but represents different concepts
- **Context matters**: Test code prefers DAMP over DRY
- **Be specific**: Exact line numbers and concrete before/after code
</avoid_overengineering>

---

## Parallel Subagent Mode (for larger codebases)

For 4+ files or >500 lines, use parallel subagents.

<use_parallel_tool_calls>
Spawn ALL analysis agents in a SINGLE message. Never serialize independent analysis work.
</use_parallel_tool_calls>

### Domains

| Domain     | Focus                        | Principles                                            |
|------------|------------------------------|-------------------------------------------------------|
| Simplicity | Complexity, over-engineering | KISS, YAGNI, Small Functions, Guard Clauses           |
| Clarity    | Readability, naming          | Cognitive Load, Self-Documenting Code, Least Surprise |
| Structure  | Organization, modularity     | DRY, Single Source of Truth, Separation of Concerns   |
| Coupling   | Dependencies, interfaces     | Encapsulation, Law of Demeter, Dependency Injection   |
| Patterns   | Design patterns              | SOLID, Command-Query Separation                       |
| Data       | State management             | Parse Don't Validate, Immutability, Idempotency       |
| Robustness | Error handling               | Fail-Fast, Design by Contract, Resilience             |
| Operations | Maintainability              | Boy Scout Rule, Observability                         |

### Subagent Template

```
You are a code quality analyzer focused on [DOMAIN] principles.

<investigate_first>
Read ALL target files completely before identifying violations.
</investigate_first>

Analyze: [FILE_PATHS]

Check for violations of: [PRINCIPLES]

Output JSON:
{
  "domain": "[domain]",
  "violations": [{
    "principle": "...",
    "location": "file.py:line",
    "problem": "...",
    "before": "...",
    "after": "...",
    "priority": "P0|P1|P2|P3"
  }]
}

ONLY report violations. Do NOT modify files.
```

### Workflow

1. **Assess** - Read files, determine relevant domains
2. **Analyze** - Spawn 2-4 agents in parallel (group related domains)
3. **Consolidate** - Deduplicate, prioritize by P0→P3
4. **Implement** - After user approval, make changes (parallelize independent edits)
