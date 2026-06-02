---
name: conductor-validator
description: Validates Conductor project artifacts for completeness,
  consistency, and correctness. Use after setup, when diagnosing issues, or
  before implementation to verify project context.
allowed-tools: Read Glob Grep Bash
metadata:
  model: opus
  color: cyan
---

# Check if conductor directory exists
ls -la conductor/

# Find all track directories
ls -la conductor/tracks/

# Check for required files
ls conductor/index.md conductor/product.md conductor/tech-stack.md conductor/workflow.md conductor/tracks.md
```

## Use this skill when

- Working on check if conductor directory exists tasks or workflows
- Needing guidance, best practices, or checklists for check if conductor directory exists

## Do not use this skill when

- The task is unrelated to check if conductor directory exists
- You need a different domain or tool outside this scope

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.
- If detailed examples are required, open `resources/implementation-playbook.md`.

## Pattern Matching

**Status markers in tracks.md:**

```
- [ ] Track Name  # Not started
- [~] Track Name  # In progress
- [x] Track Name  # Complete
```

**Task markers in plan.md:**

```
- [ ] Task description  # Pending
- [~] Task description  # In progress
- [x] Task description  # Complete
```

**Track ID pattern:**

```
<type>_<name>_<YYYYMMDD>
Example: feature_user_auth_20250115
```

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [conductor-setup](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/conductor-setup/SKILL.md) - Initialize project with Conductor artifacts (product definition, tech stack, workflow, style guides)
- [conductor-status](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/conductor-status/SKILL.md) - Display project status, active tracks, and next actions
- [context-management-context-restore](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/context-management-context-restore/SKILL.md) - Use when working with context management context restore
- [context-management-context-save](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/context-management-context-save/SKILL.md) - Use when working with context management context save
- [code-refactoring-context-restore](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/code-refactoring-context-restore/SKILL.md) - Use when working with code refactoring context restore
- [hr-pro](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/hr-pro/SKILL.md) - Professional, ethical HR partner for hiring, onboarding/offboarding, PTO and leave, performance, com...

