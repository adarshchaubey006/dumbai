# Git Flow Guardrails (Agent Reminder)

Purpose: Minimal, deterministic rules for branches, worktrees, commits, and PRs. For the full guide, see docs/dumbai/GIT_FLOW.md.

- Branch Names (deterministic)
  - Mission hub: `feat/{mission-slug}-hub`
  - Task hub: `feat/{mission-slug}/{task-slug}-hub`
  - Specialist: `feat/{mission-slug}/{task-slug}/{work-slug}-agent-{id}`
  - Slugs come from Planner; do not invent new formats.

- Worktrees (isolation)
  - One worktree per active branch. Remove after merge.
  - Keep transient files in `.feature/`; never merge `.feature/` to `develop`.

- Phase Discipline (one commit = one phase step)
  - Don’t mix CONTRACT/STUB/TEST/IMPLEMENT in a single commit.
  - STUB/TEST may include `@todo` with phase tags; remove todos before IMPLEMENT merges.

- Validation Before Push (evidence-first)
  - CONTRACT: `yarn validate <contracts>`
  - STUB: `yarn validate <implementation>`
  - TEST: `yarn validate <tests>` (tests may be skipped)
  - IMPLEMENT: `yarn validate` + targeted `yarn test`
  - VALIDATE: `yarn validate && yarn test`
  - If any step fails: STOP and escalate; do not force-push.

- Commits (scope-bound)
  - Conventional commits. Example:
    - `feat(stub): add runner stub`
      Mission: {mission-slug}
      Task: {task-slug}
      Validation: yarn validate path/to/file.ts
  - Add only your scoped files. Never use `git add -A`.

- PR Targets (merge policy)
  - Specialist → Task hub: Regular merge (preserve history)
  - Task hub → Mission hub: Regular merge (post-validate)
  - Mission hub → develop: Squash merge (after VALIDATE gate)
  - develop → main: Release squash

- Deterministic Escalations
  - Trigger on: build/test/type failures, NOT_SPECIFIED breaking policy, cross-package changes, multiple valid approaches.

- Mandatory References
  - Sequence & phase gates: `.dumbai/common/SEQUENCE_PROTOCOL.md`
  - Validation & recovery: `.dumbai/common/VALIDATION_GATES.md`
  - Full Git Flow: `docs/dumbai/GIT_FLOW.md`
