# DUMBAI Templates

This directory contains templates for the Request→Missions architecture.

## Templates

### request.md
Main request file template that tracks:
- Original user request
- Scope evolution
- Mission dependency graph
- Current mission statuses

### mission.md
Individual mission file template with:
- Frontmatter (mission slug, status, blocked_by)
- Success criteria
- Tasks
- Dependencies (blocked_by is source of truth, blocks is derived)

### scope-log.md
Brief, factual log of scope changes:
- Discoveries
- Mission additions
- Escalations
- Decisions

### EXAMPLE-update-dependencies.md
Complete example showing how a simple request evolves into multiple missions when complexity is discovered.

## Key Principles

1. **Slug-based naming**: All identifiers use kebab-case (e.g., `migrate-nextjs-api`)
2. **Date format**: ISO 8601 (YYYY-MM-DD) for all dates
3. **Request ID format**: `{date}-{request-slug}` (e.g., `2024-01-15-update-dependencies`)
4. **Single source of truth**: Only `blocked_by` in frontmatter, `blocks` is derived
5. **Deterministic escalation**: Based on quality gates, not time estimates
6. **Outcome-focused**: Track what changed, not narrative

## Mission Lifecycle States
- `planned` - Created but not started
- `in_progress` - Actively being worked on
- `blocked` - Waiting on dependency
- `escalated` - Awaiting user decision
- `completed` - All success criteria met
- `abandoned` - Cancelled or superseded

## File Ownership
| File | Owner | Modifies |
|------|-------|----------|
| request.md | Planner/Supervisor | Creates, aggregates mission status |
| mission/*.md | Planner | Creates missions |
| mission frontmatter | Supervisor | Updates status field |
| scope-log.md | Supervisor | Appends scope changes |

## Usage
Copy templates when creating new requests/missions. Replace placeholders with actual values.