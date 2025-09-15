---
name: validation_gates
description: Core validation philosophy and recovery protocols
critical: true
---

# Validation Gates & Recovery Protocol

## Core Philosophy: Tests NEVER Fail

### The Fundamental Rule
**When `yarn test` runs, it MUST be 100% green or tests should be skipped.**

- Tests that are not ready: Use `describe.skip()`
- Tests for error handling: PASS by successfully catching errors
- Tests that fail: STOP EVERYTHING - this is a critical issue

### Correct Test Patterns

```typescript
// ✅ CORRECT: Error handling test that PASSES
it('should reject invalid config', () => {
  const invalid = { wrong: 'structure' };
  // This test PASSES by verifying the error is thrown
  expect(() => processData(invalid)).toThrow(ValidationError);
});

// ✅ CORRECT: Skipped test during STUB phase
describe.skip('processData', () => {
  it('should process valid config', () => {
    // Will fail during STUB phase, so we skip it
    const result = processData(validConfig);
    expect(result).toMatchSchema(ResultSchema);
  });
});

// ❌ WRONG: Test that fails
it('should work', () => {
  const result = processData(config);
  expect(result).toBe(5); // Fails because result is 3
  // THIS IS A PROBLEM - FIX THE CODE OR FIX THE TEST
});
```

## Trust but Verify Protocol

### For Supervisor

Even when specialists report success, INDEPENDENTLY VERIFY:

```typescript
interface VerificationProtocol {
  // 1. Verify files were actually modified
  checkGitStatus(): ModifiedFiles;

  // 2. Re-run validation on claimed files
  validateFiles(files: string[]): ValidationResult;

  // 3. Re-run tests if claimed to pass
  runTests(testFiles: string[]): TestResult;

  // 4. Check for false-fixes
  detectTestManipulation(): FalseFixReport;

  // 5. Verify phase gates
  checkPhaseCompletion(phase: Phase): boolean;
}
```

**Implementation**:
```bash
# Supervisor verification sequence
echo "=== VERIFYING SPECIALIST REPORT ==="

# 1. Check what actually changed
git diff --name-only HEAD~1

# 2. Validate modified files
yarn validate $(git diff --name-only HEAD~1 | grep -E '\.(ts|tsx)$')

# 3. Run relevant tests
yarn test $(git diff --name-only HEAD~1 | grep -E '\.test\.(ts|tsx)$')

# 4. Check for test manipulation
git diff HEAD~1 -- '*.test.ts' | grep -E '^\+.*expect\(' && echo "⚠️ TEST MODIFIED"

echo "✅ Verification complete"
```

### For Coordinator

Before reporting phase completion to Supervisor:

```typescript
interface PhaseGateVerification {
  CONTRACT: {
    gate: "All schemas compile with zero errors",
    verify: () => yarn.validate('src/schemas/*.ts') === 0
  },
  STUB: {
    gate: "All stubs type-check against contracts",
    verify: () => yarn.validate('src/*.ts') === 0
  },
  TEST: {
    gate: "Test structure valid, all tests skipped",
    verify: () => {
      const valid = yarn.validate('test/*.ts') === 0;
      const allSkipped = !testFiles.some(f => !f.includes('.skip'));
      return valid && allSkipped;
    }
  },
  IMPLEMENT: {
    gate: "All tests pass, zero TODOs",
    verify: () => {
      const testsPass = yarn.test() === 0;
      const noTodos = !grep('TODO|FIXME', 'src/**/*.ts');
      return testsPass && noTodos;
    }
  }
}
```

## Recovery Protocol for Quality Gate Violations

### When Tests Fail (This WILL happen)

```typescript
interface RecoveryProtocol {
  detection: "Test failure detected",

  immediateActions: [
    "STOP all parallel work",
    "Prevent commits",
    "Alert all specialists"
  ],

  diagnosis: {
    checkImplementationBug: () => "Review implementation against requirements",
    checkTestCorrectness: () => "Verify test expectations match requirements",
    checkContractMismatch: () => "Validate contracts align with implementation",
    checkDependencies: () => "Ensure all dependencies resolved",
    checkPhaseSequence: () => "Verify phases completed in order"
  },

  recovery: {
    implementationBug: "Fix implementation, re-validate",
    testBug: "Fix test (scrutinize for false-fixes), re-validate",
    contractMismatch: "Escalate for contract evolution",
    missingDependency: "Complete dependent work first",
    outOfSequence: "Rollback and follow phase order"
  },

  documentation: {
    what: "Exact failure message",
    why: "Root cause analysis",
    how: "Steps taken to fix",
    prevention: "How to prevent recurrence"
  },

  verification: [
    "Run full validation suite",
    "Verify no regressions",
    "Check all phase gates",
    "Confirm fix addresses root cause"
  ]
}
```

### Practical Recovery Steps

```bash
# 1. STOP - Prevent further damage
echo "❌ QUALITY GATE VIOLATION - STOPPING ALL WORK"

# 2. ASSESS - What failed?
yarn test --verbose 2>&1 | tee test-failure.log

# 3. ROLLBACK if needed
git status
git diff
# If changes are problematic:
git reset --hard HEAD~1

# 4. FIX - Address root cause
# Option A: Fix implementation
vim src/broken-file.ts
yarn validate src/broken-file.ts

# Option B: Fix test (WITH SCRUTINY)
vim test/broken.test.ts
# CHECK: Are we weakening assertions?
# CHECK: Are we changing expectations to match bugs?

# 5. VERIFY - Full validation
yarn validate
yarn test
grep -r "TODO\|FIXME" src/

# 6. DOCUMENT - Record what happened
cat >> recovery-log.md << EOF
## Recovery from Test Failure - $(date)
- **What**: Test xyz failed with error ABC
- **Why**: Implementation didn't handle edge case
- **Fix**: Added null check in processData
- **Prevention**: Add edge case to test suite earlier
EOF

# 7. PROCEED - Only after full green
echo "✅ Recovery complete - all gates passing"
```

## Commit Protocol for All Specialists

### Standard Commit After Validation

```bash
# 1. Validate your changes
yarn validate path/to/your/files || exit 1

# 2. Run relevant tests
yarn test path/to/your/tests || exit 1

# 3. Stage ONLY your files
git add path/to/your/specific/files
# NEVER: git add -A
# NEVER: git add .

# 4. Commit with context
git commit -m "feat(MODULE): Implement FEATURE per TASK_ID

- Added CONTRACT/STUB/TEST/IMPLEMENT for FEATURE
- Validation: All passing
- Tests: All green
- Phase: PHASE_NAME complete"

# 5. Handle parallel commit races
if git push fails due to race:
  git pull --rebase
  yarn validate  # Re-validate after rebase
  git push
```

## Phase-Specific Validation Commands

### For Implementation Specialist
```bash
CONTRACT: yarn validate src/schemas/*.ts
STUB:     yarn validate src/*.ts
TEST:     yarn validate test/*.ts  # If writing tests
IMPLEMENT: yarn validate src/*.ts && yarn test
```

### For Test Writer Specialist
```bash
TEST:     yarn validate test/*.ts
VERIFY:   yarn test test/*.ts  # After unskipping
```

### For Documentation Specialist
```bash
UPDATE:   yarn validate  # Ensure no syntax errors
VERIFY:   grep -r "TODO\|FIXME" . | grep -v node_modules
```

### For Integration Specialist
```bash
INTEGRATE: yarn validate packages/*/src/*.ts
VERIFY:    yarn test:integration
```

## Critical Reminders

1. **Tests NEVER fail** - Use skip, don't let them fail
2. **Trust but verify** - Always double-check claims
3. **Recovery is normal** - Have a plan when things go wrong
4. **Document failures** - Learn from what went wrong
5. **Commit as checkpoint** - Each validation creates a save point
6. **Git Flow guardrails** - `.dumbai/common/GIT_FLOW_GUARDRAILS.md` (brief); full policy: `docs/dumbai/GIT_FLOW.md`