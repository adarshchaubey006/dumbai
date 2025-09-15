---
name: sequence_protocol
description: Mandatory execution sequence and validation gates for all agents
critical: true
---

See also: `docs/dumbai/GIT_FLOW.md` (full policy) and `.dumbai/common/GIT_FLOW_GUARDRAILS.md` (one-page reminder) 
for deterministic branch/worktree/commit rules that enforce these phase gates.

# DUMBAI Execution Sequence & Validation Protocol

## Core Philosophy
- **Code is truth** - Never trust comments, commit messages, or TODO content
- **TODO Trust Policy**:
  - TRUST: @todo tag structure for phase tracking (`@todo[STUB]`, `@todo[TEST]`)
  - VERIFY: TODO content accuracy (what it claims needs doing)
- **Challenge everything** - TODO descriptions aren't necessarily accurate
- **NOT to please the user** - Ensure correctness over speed
- **Reserved context for validation** - Use extensive verification before proceeding

## Mandatory Development Sequence

### Phase Flow (STRICT ORDER)
```
RESEARCH → CONTRACT → VALIDATE → STUB → VALIDATE → TEST → VALIDATE → IMPLEMENT → VALIDATE → REVIEW
```

Each phase MUST complete with validation before proceeding:

### RESEARCH Phase (Build vs Buy Decision)
```typescript
// Before writing ANY code, check if solution already exists
const decision = await research_specialist.evaluate({
  need: "date parsing library",
  requirements: ["timezone support", "ISO8601", "relative dates"]
});
// Returns: USE (npm package) | WRAP (with adapter) | BUILD (custom)
```
**Validation Gate**: Document decision in mission frontmatter
**Proceed only if**: Clear USE/WRAP/BUILD decision made

### CONTRACT Phase
```typescript
// Create types, interfaces, schemas
export const ConfigSchema = z.object({...});
export type Config = z.infer<typeof ConfigSchema>;
```
**Validation Gate**: `yarn validate path/to/contract.ts`
**Proceed only if**: Zero type errors

### 2. STUB Phase
```typescript
// Correct signatures, throw NotImplementedError
export function processData(config: Config): Result {
  /**
   * @todo [#123][STUB] Implement data processing
   * @contract ConfigSchema
   */
  throw new NotImplementedError('processData');
}
```
**Validation Gate**: `yarn validate path/to/implementation.ts`
**Proceed only if**: Types align with contracts

### 3. TEST Phase (Skipped Tests)
```typescript
describe.skip('processData', () => {
  it('should process valid config', () => {
    const result = processData(validConfig);
    expect(result).toMatchSchema(ResultSchema);
  });
});
```
**Validation Gate**: `yarn validate path/to/implementation.ts path/to/test.ts`
**Proceed only if**: Test structure valid, contracts consistent

### 4. IMPLEMENT Phase
```typescript
// Replace stub with actual implementation
export function processData(config: Config): Result {
  const validated = ConfigSchema.parse(config);
  // Actual implementation
  return result;
}
```
**Validation Gates**:
1. `yarn validate path/to/implementation.ts path/to/test.ts`
2. `yarn test path/to/test.ts` (after unskipping)
3. `yarn test` (full suite)

### 5. REVIEW Phase (MANDATORY)
Load code-reasoning tool and perform thorough review

## TODO Policy

### When TODOs are ALLOWED
```typescript
/**
 * @todo [#123][STUB] Implement within current burst
 * ALLOWED: Will be addressed in THIS work session
 */
```

Conditions:
1. **In STUB phase only** - Marking unimplemented functions
2. **With issue reference** - `[#123]` for tracking
3. **With phase tag** - `[STUB]`, `[TEST]` for clarity
4. **Guaranteed resolution** - Will be fixed in current burst/mission

### When TODOs are FORBIDDEN
```typescript
// ❌ FORBIDDEN: Leaving for "later"
// TODO: Optimize this someday

// ❌ FORBIDDEN: Vague or untracked
// TODO: Handle edge cases

// ❌ FORBIDDEN: In completed phases
export function implemented() {
  // TODO: Make this better  <-- NO!
}
```

## Self-Assessment Protocol

### Before Marking ANY Task Complete

```typescript
interface CompletionChecklist {
  // 1. Validation Passes
  validateCommand: 'yarn validate <files>';
  validateResult: 'MUST be zero errors';

  // 2. Tests Pass
  testCommand: 'yarn test <files>';
  testResult: 'ALL tests green (except allowed skips)';

  // 3. No False-Fixes
  testIntegrity: 'Tests NOT modified to pass';
  expectations: 'Assertions match requirements';

  // 4. No TODOs in Implementation
  todoScan: 'grep -n "TODO\\|FIXME" <implemented-files>';
  todoResult: 'Zero matches in IMPLEMENT phase';

  // 5. Documentation Current
  jsDocComplete: 'All public APIs documented';
  jsDocAccurate: 'Docs match implementation exactly';
}
```

### False-Fix Detection

**RED FLAGS to check:**
```typescript
// ❌ Test modified to match bug
- expect(result).toBe(5);  // Original
+ expect(result).toBe(3);  // Changed to pass!

// ❌ Test logic removed
- expect(() => func()).toThrow();
+ // expect(() => func()).toThrow();  // Commented out!

// ❌ Test weakened
- expect(result).toEqual(complexObject);
+ expect(result).toBeDefined();  // Now always passes!
```

## Parallel Specialist Coordination

### Task Breakdown Requirements
```yaml
Task Criteria:
  - Deterministic: Clear inputs/outputs
  - Isolated: No dependencies on other parallel tasks
  - Bounded: <150 LoC or 3 functions
  - Validated: Has clear success criteria
  - Documented: Includes file paths and context
```

### Specialist Spawn Pattern
```typescript
// Spawn multiple specialists in ONE message
const specialists = [
  { type: "implementation_specialist", task: "implement-auth", files: ["src/auth.ts"], phase: "STUB" },
  { type: "implementation_specialist", task: "implement-validation", files: ["src/validate.ts"], phase: "STUB" },
  { type: "test_writer_specialist", task: "write-auth-tests", files: ["test/auth.test.ts"], phase: "TEST" }
];
// All spawn in parallel via Supervisor, no waiting
```

Note: "Worker" in user prompts = "Specialist" in DUMBAI terminology

## Supervisor Review Responsibilities

### MUST Challenge:
1. **Scope Creep**: "Is this really needed?"
2. **Duplications**: "Doesn't this already exist?"
3. **Complexity**: "Can this be simpler?"
4. **TODOs**: "Are these real or assumptions?"
5. **Tests**: "Do these actually test the requirement?"

### MUST Verify:
1. Recent changes: `git log --oneline -20`
2. Modified files pass: `yarn validate <each-file>`
3. Test coverage adequate: Not just line coverage
4. No code smells: Duplication, dead code, magic numbers
5. JSDoc complete and accurate

## Escalation Triggers

### IMMEDIATE STOP Conditions:
```typescript
const STOP_AND_ESCALATE = {
  ambiguous_requirements: "Scope unclear",
  validation_failures: "yarn validate fails",
  test_failures: "yarn test fails",
  false_fixes_detected: "Tests modified to pass",
  circular_dependencies: "A needs B needs A",
  scope_explosion: ">3x original estimate",
  todo_accumulation: ">5 TODOs in single file"
};
```

## Recovery Protocol

When returning after interruption:
1. Check git status for uncommitted work
2. Run `yarn validate` on all modified files
3. Run `yarn test` to verify state
4. Review recent commits for false-fixes
5. Resume from last validated phase

## Success Criteria

A task is ONLY complete when:
- ✅ Zero validation errors
- ✅ All tests pass (except approved skips)
- ✅ No TODOs in implemented code
- ✅ Documentation matches implementation
- ✅ No false-fixes detected
- ✅ Code review passed
- ✅ No code smells introduced

## Example Complete Sequence

```bash
# 1. CONTRACT
echo "Creating contract..."
# ... create schema files
yarn validate src/schemas/*.ts || exit 1

# 2. STUB
echo "Creating stubs..."
# ... create stubbed implementations
yarn validate src/*.ts || exit 1

# 3. TEST
echo "Writing tests..."
# ... create skipped test suites
yarn validate src/*.ts test/*.ts || exit 1

# 4. IMPLEMENT
echo "Implementing..."
# ... replace stubs with implementation
yarn validate src/*.ts test/*.ts || exit 1

# 5. VALIDATE
echo "Running tests..."
yarn test test/*.ts || exit 1

# 6. REVIEW
echo "Final review..."
grep -n "TODO\|FIXME" src/*.ts && echo "ERROR: TODOs found!" && exit 1
yarn test || exit 1
yarn validate || exit 1

echo "✅ All validation gates passed!"
```

## Critical Reminders

1. **Specialists need extreme clarity** - Tasks must be deterministic and bounded
2. **Parallel isn't always better** - Dependencies force sequence
3. **Trust nothing** - Verify everything with actual commands
4. **Code is truth** - Implementation defines the spec
5. **Challenge the user** - Correctness over compliance

## Tool Access Note

While user prompts reference "code-reasoning MCP", specialists in DUMBAI have bounded tool access:
- Implementation specialists: File modification within scope
- Test specialists: Test file creation/modification
- Documentation specialists: Doc file updates
- Supervisor: Orchestration and review (may have extended tool access)