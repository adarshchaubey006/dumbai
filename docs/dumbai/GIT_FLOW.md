# Contributing to MCP Funnel

This guide defines the git-focused contribution workflow **within** the Deterministic Unified Management of Behavioral AI agents (**DUMBAI**) framework. Every git action must reinforce deterministic execution, phase discipline, and Request→Missions orchestration.

---

## Core Principles

- **Request→Missions governs scope**: every branch maps to a mission inside `.dumbai/requests/*/missions/`. Git work happens only after a mission is in `in_progress` and dependencies are clear.
- **DUMBAI phases drive branch state**: commits capture phase progression (CONTRACT → STUB → TEST → IMPLEMENT → VALIDATE). Never mix phases in one commit.
- **Single-writer discipline**: Supervisor owns mission frontmatter; contributors respect append-only discovery queues and deterministic escalation triggers.
- **Deterministic evidence**: validation commands and test results are recorded before pushing. No branch advances without green gates defined in `.dumbai/common/SEQUENCE_PROTOCOL.md` and `.dumbai/common/VALIDATION_GATES.md`.

---

## Request→Missions to Branch Translation

1. **Planner creates mission** with slug `mission-slug`.
2. **Supervisor sets status** to `in_progress` and assigns specialists.
3. **Coordinator decomposes work** into deterministic tasks (≤150 LoC, ≤3 functions) with validation commands.
4. **Git branch is created** only after the above steps. Branch names encode mission, task, and specialist role.

```
main
└── develop
    └── feat/{mission-slug}-hub                     # Feature hub (supervisor controlled)
        └── feat/{mission-slug}/{task-slug}-hub     # Coordinator-level task hub
            └── feat/{mission-slug}/{task-slug}/{work-slug}-agent-{id}  # Specialist branch
```

- `{mission-slug}` = Mission identifier from frontmatter (kebab case).
- `{task-slug}` = Coordinator-assigned task (kebab case).
- `{work-slug}` = Specific work burst (e.g., `contract`, `stub-runner`, `tests`).
- `{id}` = Sequential specialist id (1-based).

**Example**
```
feat/migrate-nextjs-api-hub
└── feat/migrate-nextjs-api/implementation-hub
    ├── feat/migrate-nextjs-api/implementation/stub-runner-agent-1
    └── feat/migrate-nextjs-api/implementation/tests-agent-1
```

---

## Worktree Pattern

Use separate worktrees to isolate missions and phases:

```bash
# Create mission hub (supervisor)
git checkout develop
git checkout -b feat/migrate-nextjs-api-hub

# Coordinator task hub
git worktree add ../migrate-impl-hub -b feat/migrate-nextjs-api/implementation-hub feat/migrate-nextjs-api-hub

# Specialist burst
git worktree add ../migrate-stub-burst \
  -b feat/migrate-nextjs-api/implementation/stub-runner-agent-1 \
  feat/migrate-nextjs-api/implementation-hub
```

- One worktree per active branch. Remove (`git worktree remove …`) immediately after merge.
- `.feature/` directory holds transient coordination artifacts; never allow it into `develop`.

---

## Phase-Aligned Branch Lifecycle

### 1. Mission Hub (`feat/{mission}-hub`)
- Owner: Supervisor.
- Purpose: Integrate task hubs, enforce breaking-change policy, maintain mission state.
- Merge: Squash into `develop` only after VALIDATE gate passes.

### 2. Task Hub (`feat/{mission}/{task}-hub`)
- Owner: Coordinator.
- Purpose: Merge specialist bursts for a single task. Maintains deterministic execution order.
- Merge: Regular merges from specialist branches; squash when merging upward.

### 3. Specialist Branch (`feat/{mission}/{task}/{work}-agent-{id}`)
- Owner: Specialist (implementation/test/documentation/etc.).
- Purpose: Execute a single work burst tied to one DUMBAI phase.
- Lifecycle:
  1. `git fetch` hub branch
  2. Implement burst respecting phase limits
  3. Run validation commands (see below)
  4. Commit with conventional message + phase metadata
  5. Push & open PR to task hub
  6. Delete branch post-merge

---

## Validation Gates per Branch Level

| Level | Responsible | Required Commands |
|-------|-------------|-------------------|
| Specialist | Implementation/Test/Documentation | `yarn validate <files>` then phase-specific commands (e.g., `yarn test <files>`) before pushing |
| Task Hub | Coordinator | Re-run validations across merged files; ensure no TODO violations; confirm discoveries processed |
| Mission Hub | Supervisor | Full `yarn validate` + `yarn test`; verify Request→Missions dependencies before squashing |
| Develop → Main | Release owner | Full suite + changelog + mission completion review |

**Failure Handling**: Follow `.dumbai/common/VALIDATION_GATES.md` recovery protocol. If any command fails, STOP and escalate instead of force-pushing.

---

## Commit Rules

- Conventional commits with optional phase prefix: `feat(stub): …`, `test(contract): …`, `docs(phase): …`.
- Each commit covers exactly one DUMBAI phase step for its scope. Do not mix CONTRACT and STUB work in a single commit.
- Include mission id and task slug in body for traceability:
  ```
  feat(stub): add runner stub
  
  Mission: migrate-nextjs-api
  Task: implementation/stub-runner
  Validation: yarn validate packages/cli/src/runner.ts
  ```
- No `git add -A`. Add only files within assigned scope.
- TODO policy: only phase-tagged (`@todo [#123][STUB] …`) TODOs in STUB/TEST phases; remove before IMPLEMENT commits merge.

---

## Pull Request Flow

### Specialist → Task Hub PR
- Target: `feat/{mission}/{task}-hub`
- Checklist:
  - Mission frontmatter status `in_progress`
  - Phase JSDoc tags present
  - Validation logs attached (e.g., command + exit 0)
  - Discoveries appended to mission frontmatter (append-only)
- Reviewer: reviewer_specialist
- Merge: Regular merge to preserve granular history for Coordinator.

### Task Hub → Mission Hub PR
- Target: `feat/{mission}-hub`
- Requirements:
  - All specialist branches merged and deleted
  - Coordinator confirms dependencies satisfied
  - Validation rerun across combined scope
  - Outstanding discoveries processed or escalated
- Merge: Regular merge (preserve history) to aid mission-level diagnosis.

### Mission Hub → develop PR
- Target: `develop`
- Requirements:
  - Mission status `completed`
  - Request outcomes updated in `.dumbai/requests/.../request.md`
  - Scope log entry added for completion
  - Squash merge using `scripts/squash-merge.sh feat/{mission}-hub`
  - `.feature/` filtered automatically
- Commit message template:
  ```
  feat: complete {mission-slug}
  
  - Phase progression: CONTRACT → … → VALIDATE
  - Validation: yarn validate && yarn test
  - Breaking changes: {REQUIRED|NOT_REQUIRED|NOT_SPECIFIED → escalate}
  ```

### develop → main
- Follow release protocol. Only release-ready missions; no `.feature/` or partial phases.

---

## Deterministic Conflict Resolution

1. Detect early via Coordinator diff reviews.
2. If multiple specialists need same file, sequence bursts instead of parallelizing.
3. Use mission discoveries to log required reruns after contract evolution.
4. Never resolve conflicts directly on `develop`; use mission/task hubs.

---

## Checklist Before Pushing Any Branch

1. Mission status is `in_progress` and not blocked.
2. Phase-specific validation commands have succeeded locally:
   - CONTRACT: `yarn validate contracts/...`
   - STUB: `yarn validate <implementation>`
   - TEST: `yarn validate <tests>` (tests skipped)
   - IMPLEMENT: `yarn validate` + targeted `yarn test`
   - VALIDATE: `yarn validate && yarn test`
3. JSDoc tags comply with DUMBAI matrix (including @phase, @todo, @blocked-by, @since as applicable).
4. Discoveries appended to mission frontmatter with ISO timestamps.
5. No `.feature/` or local coordination files staged.
6. Branch naming matches hierarchy; worktree cleaned when done.

---

## Reference Materials

- `.dumbai/common/SEQUENCE_PROTOCOL.md` – Phase order & validation requirements.
- `.dumbai/common/VALIDATION_GATES.md` – Recovery from validation failures.
- `docs/dumbai/README.md` – DUMBAI philosophy and Request→Missions architecture.
- `.dumbai/templates/*` – Mission and documentation templates for consistency.

Stay deterministic: if any ambiguity remains (e.g., breaking change policy `NOT_SPECIFIED`), escalate before creating or merging branches.
