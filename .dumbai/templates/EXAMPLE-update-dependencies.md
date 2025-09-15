# EXAMPLE: Update Dependencies Request

This example shows how a simple "update dependencies" request evolves into multiple missions when breaking changes are discovered.

## Directory Structure
```
.dumbai/requests/2024-01-15-update-dependencies/
├── request.md
├── scope-log.md
└── missions/
    ├── audit-dependencies.md
    ├── update-minor-versions.md
    ├── migrate-nextjs-api.md      # Added when scope expanded!
    └── fix-breaking-tests.md      # Added when tests failed!
```

## request.md Example
```markdown
# Request: Update Dependencies

## Original Request
"Please update package.json dependencies to latest versions"

## Current Scope
- ✅ Update all minor/patch versions
- ⚠️ NextJS 13→14 requires API migration (scope expanded)
- 🔄 Fixing 17 breaking tests

## Outcomes
- [x] Dependencies audited for vulnerabilities
- [x] Security vulnerabilities fixed (3 critical)
- [ ] NextJS migration complete
- [ ] All tests passing

## Scope Changes
- 2024-01-15 11:30: NextJS 14 has breaking API changes [reason: discovered during update]
- 2024-01-15 14:00: 17 tests failing after updates [reason: API changes]

## Decision Log
- Chose NextJS App Router over Pages Router [approved: user]
- Deferred React 19 upgrade [reason: too many breaking changes]
```

## missions/migrate-nextjs-api.md Example
```markdown
---
mission: migrate-nextjs-api
status: escalated
blocked_by: [update-minor-versions]
created: 2024-01-15
---

# Mission: migrate-nextjs-api

## Objective
Migrate application from NextJS 13 Pages Router to NextJS 14 App Router

## Success Criteria
- [ ] All pages migrated to app/ directory
- [ ] API routes converted to new format
- [ ] Middleware updated for new structure
- [ ] Build succeeds without errors

## Tasks
- [ ] Move pages/* to app/*
- [ ] Convert getServerSideProps to Server Components
- [ ] Update API route handlers to new format
- [ ] Update middleware.ts for new conventions
- [ ] Fix import paths throughout codebase

## Dependencies
- **Requires**: update-minor-versions (must update to NextJS 14 first)
- **Blocks**: [AUTO-DERIVED: fix-auth-tests, fix-e2e-tests]

## Scope Boundaries

### Allowed:
- Modify pages/* and app/* directories
- Update API route handlers
- Modify middleware.ts
- Update import statements
- Follow NextJS 14 migration guide

### Not Allowed:
- Modify package.json (already updated by previous mission)
- Change non-NextJS related code
- Add new features during migration
- Modify test files (separate mission)
- Alter build configuration

## Escalation Details
**Status**: ESCALATED
**Trigger**: quality_gate_failed.build_broken
**Error**: Module not found: 'next/navigation'
**Options**:
1. Rollback to NextJS 13 (abandon mission)
2. Fix imports manually (47 files affected)
3. Use @next/codemod tool (requires new dependency)
```

## scope-log.md Example
```markdown
# Scope Evolution Log

## 2024-01-15 10:00
- Started: Update dependencies
- Discovered: 47 packages outdated
- Estimate: 2 hours

## 2024-01-15 11:30
- Discovered: NextJS 14 breaking changes
- Added: Mission migrate-nextjs-api
- Impact: +1 mission, ~1 day

## 2024-01-15 14:00
- Discovered: 17 tests failing
- Added: Mission fix-breaking-tests
- Impact: +1 mission, ~4 hours

## 2024-01-15 14:30
- Escalated: Build failure in migrate-nextjs-api
- Options: 3 approaches presented
- Decision: Pending user input

## 2024-01-15 15:00
- Deferred: React 19 upgrade to separate request
- Reason: Too many breaking changes for current scope
```

## Key Patterns Demonstrated

1. **Scope Evolution**: Simple request → Complex multi-mission project
2. **Deterministic Escalation**: Build failure triggers user decision
3. **Mission Dependencies**: Clear blocking relationships
4. **Outcome Focus**: Brief, factual logging
5. **Slug Consistency**: All identifiers use kebab-case