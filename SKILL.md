---
name: self-improving-agent
description: "Capture learnings, errors, and feature requests for continuous improvement. Use when: (1) A command or operation fails, (2) User corrects you, (3) User requests a missing capability, (4) An external API/tool fails, (5) Better approach discovered for recurring task. Review before major tasks."
metadata: {"nanobot":{"emoji":"📚"}}
---

# Self-Improvement

Log learnings and errors to `.learnings/` for continuous improvement.

## Setup

Ensure `.learnings/` exists in workspace root:

```bash
mkdir -p .learnings
[ -f .learnings/LEARNINGS.md ] || printf "# Learnings\n\n---\n" > .learnings/LEARNINGS.md
[ -f .learnings/ERRORS.md ] || printf "# Errors\n\n---\n" > .learnings/ERRORS.md
[ -f .learnings/FEATURE_REQUESTS.md ] || printf "# Feature Requests\n\n---\n" > .learnings/FEATURE_REQUESTS.md
```

## Quick Reference

| Situation | Action |
|-----------|--------|
| Command fails | Log to `.learnings/ERRORS.md` |
| User corrects you | Log to `.learnings/LEARNINGS.md` (category: `correction`) |
| User wants missing feature | Log to `.learnings/FEATURE_REQUESTS.md` |
| API/tool fails | Log to `.learnings/ERRORS.md` |
| Knowledge outdated | Log to `.learnings/LEARNINGS.md` (category: `knowledge_gap`) |
| Found better approach | Log to `.learnings/LEARNINGS.md` (category: `best_practice`) |

**Never log secrets, tokens, or full source files.**

## Entry Format

### Learning Entry (`.learnings/LEARNINGS.md`)

```markdown
## [LRN-YYYYMMDD-XXX] category

**Logged**: ISO-8601 timestamp
**Priority**: low | medium | high | critical
**Status**: pending
**Area**: frontend | backend | infra | tests | docs | config

### Summary
One-line description

### Details
What happened, what was wrong, what's correct

### Suggested Action
Specific fix or improvement

### Metadata
- Source: conversation | error | user_feedback
- Related Files: path/to/file.ext
- Tags: tag1, tag2
---
```

Categories: `correction` | `insight` | `knowledge_gap` | `best_practice`

### Error Entry (`.learnings/ERRORS.md`)

```markdown
## [ERR-YYYYMMDD-XXX] command_name

**Logged**: ISO-8601 timestamp
**Priority**: high
**Status**: pending
**Area**: backend

### Summary
What failed

### Error
\`\`\`
Error message or output
\`\`\`

### Context
- Command attempted
- Environment details

### Suggested Fix
What might resolve this

### Metadata
- Reproducible: yes | no | unknown
- Related Files: path/to/file.ext
---
```

### Feature Request Entry (`.learnings/FEATURE_REQUESTS.md`)

```markdown
## [FEAT-YYYYMMDD-XXX] capability_name

**Logged**: ISO-8601 timestamp
**Priority**: medium
**Status**: pending
**Area**: frontend

### Requested Capability
What the user wanted

### User Context
Why they needed it

### Complexity Estimate
simple | medium | complex

### Suggested Implementation
How this could be built
---
```

## Resolving Entries

When fixed, update:
1. `**Status**: pending` → `**Status**: resolved`
2. Add resolution block:

```markdown
### Resolution
- **Resolved**: 2025-01-16T09:00:00Z
- **Commit/PR**: abc123
- **Notes**: What was done
```

Other statuses: `in_progress`, `wont_fix`, `promoted`

## Promotion

When a learning is broadly applicable, promote to project memory files.

### Promotion Targets

| Target | Content |
|--------|---------|
| `CLAUDE.md` | Project facts, conventions, gotchas |
| `AGENTS.md` | Workflows, tool patterns, automation |
| `.github/copilot-instructions.md` | Project context for Copilot |

### How to Promote

1. Distill learning into concise rule
2. Add to appropriate file
3. Update entry: `**Status**: promoted`, add `**Promoted**: CLAUDE.md`

## Recurring Patterns

When logging something similar to existing entry:

1. Search first: `grep -r "keyword" .learnings/`
2. Link: Add `**See Also**: ERR-20250110-001` in Metadata
3. Bump priority if recurring
4. Consider systemic fix or promotion

## Detection Triggers

**Corrections** (→ learning, category `correction`):
- "No, that's not right..."
- "Actually, it should be..."
- "That's outdated..."

**Feature Requests**:
- "Can you also..."
- "I wish you could..."
- "Is there a way to..."

**Knowledge Gaps** (→ learning, category `knowledge_gap`):
- User provides unknown information
- Documentation is outdated
- API behavior differs from understanding

**Errors**:
- Non-zero exit code
- Exception/stack trace
- Timeout or connection failure

## Periodic Review

Check `.learnings/` before major tasks:

```bash
# Count pending
grep -h "Status\*\*: pending" .learnings/*.md | wc -l

# List high-priority
grep -B5 "Priority\*\*: high" .learnings/*.md | grep "^## \["

# Find by area
grep -l "Area\*\*: backend" .learnings/*.md
```

Review actions: resolve fixed items, promote learnings, link related entries, escalate recurring issues.

## Gitignore

Keep learnings local (default):
```gitignore
.learnings/
```

Track in repo (team-wide): Don't add to .gitignore.

## Extract to Skill

When a learning is valuable enough to become a reusable skill, use skill-creator.

**Extraction criteria** (any of):
- Has `See Also` links to 2+ similar issues
- Status `resolved` with working fix
- Non-obvious discovery
- Broadly applicable
- User flags: "save this as a skill"

After extraction: Set status `promoted_to_skill`, add `Skill-Path` metadata.
