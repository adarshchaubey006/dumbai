---
# Mission Metadata (Supervisor maintains this section)
mission: {kebab-case-mission-slug}
status: planned  # planned|in_progress|blocked|escalated|completed|abandoned
blocked_by: []   # [other-mission-slug, another-mission]
backward_compatibility: NOT_SPECIFIED  # REQUIRED|NOT_REQUIRED|NOT_SPECIFIED
created: {YYYY-MM-DD}T{HH:MM:SS}Z
last_updated: {YYYY-MM-DD}T{HH:MM:SS}Z

# Breaking Changes Decision (Planner sets, Supervisor validates)
# - REQUIRED: Maintain compatibility, add deprecation warnings
# - NOT_REQUIRED: Allow breaking changes, provide migration guide
# - NOT_SPECIFIED: MUST escalate to user before proceeding

# Phase & Progress Tracking (Supervisor updates)
current_phase: null  # null|RESEARCH|CONTRACT|STUB|TEST|IMPLEMENT|VALIDATE
phases_completed: []
work_bursts_completed: 0
last_checkpoint: null

# Specialist Assignments (Supervisor manages)
specialist_assignments: {}
# Example when populated:
#   impl_1:
#     type: implementation_specialist
#     assigned_files: [src/runner.ts, src/utils.ts]
#     current_file: src/runner.ts
#     current_phase: STUB
#     lines_modified: 0
#     last_activity: null
#     burst_in_progress: false

# Discovery Queue (Specialists append, Supervisor processes)
discoveries: []
# Example entry:
#   - timestamp: 2024-01-15T14:15:00Z
#     specialist: impl_1
#     type: contract_gap
#     contract: VitestArgsSchema
#     issue: "Missing field: includeStderr"
#     status: pending_review
#     burst_id: burst_3

# Contract Evolution Events (Supervisor maintains)
contract_events: []
# Example entry:
#   - timestamp: 2024-01-15T14:22:00Z
#     type: CONTRACT_UPDATE
#     affected_specialists: [impl_1, test_1]
#     contract: VitestArgsSchema
#     resolution: Updated schema with includeStderr field
#     next_execution: impl_1 with updated contract

# Validation Cache (Updated after each validation run)
validation_cache:
  last_run: null
  command: null
  files_validated: {}
  errors: []
---

# Mission: {kebab-case-mission-slug}

## Objective
{Clear, concise description of what this mission accomplishes}

## Success Criteria
- [ ] {Specific, measurable, verifiable outcome}
- [ ] {Another specific outcome with clear completion definition}
- [ ] {Third measurable outcome}

## Phase Structure

### Phase 1: CONTRACT
**Gate**: All schemas/types compile with zero errors
- [ ] Define input schemas/types
- [ ] Define output schemas/types
- [ ] Validation: `yarn validate src/schemas/*.ts`

### Phase 2: STUB
**Gate**: All stubs type-check against contracts
- [ ] Create function signatures
- [ ] Add @todo tags with issue refs
- [ ] Validation: `yarn validate src/*.ts`

### Phase 3: TEST
**Gate**: Test structure valid, imports resolve
- [ ] Write skipped test suites
- [ ] Add @blocked-by tags
- [ ] Validation: `yarn validate test/*.ts`

### Phase 4: IMPLEMENT
**Gate**: All tests pass, zero TODOs in implementation
- [ ] Replace stubs with implementation
- [ ] Remove @todo tags
- [ ] Validation: `yarn test`

### Phase 5: VALIDATE
**Gate**: Full suite passes, no regressions
- [ ] Run full validation suite
- [ ] Check for TODOs/FIXMEs
- [ ] Validation: `yarn validate && yarn test`

## Dependencies
- **Requires**: {other-mission-slug} ({reason why it must complete first})
- **Blocks**: [AUTO-DERIVED by Supervisor from other missions' blocked_by]

## Scope Boundaries

### Allowed:
- {Specific files/directories that can be modified}
- {Types of changes permitted}
- {Resources that can be used}
- {Patterns that should be followed}

### Not Allowed:
- {Files/directories that must NOT be modified}
- {Types of changes that are forbidden}
- {Resources that cannot be accessed}
- {Patterns that must be avoided}

### Examples:
#### For implementation mission:
- Allowed: Modify src/lib/validation/*, Add unit tests
- Not Allowed: Change build configuration, Add new dependencies

#### For refactoring mission:
- Allowed: Restructure internal code, Update imports
- Not Allowed: Change public API, Modify contracts

## Technical Details
{Any specific technical requirements, constraints, or considerations}

## Acceptance Tests
- {How to verify success criteria 1}
- {How to verify success criteria 2}
- {How to verify success criteria 3}

## Notes
{Any additional context, risks, or considerations}