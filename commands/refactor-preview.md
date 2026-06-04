# /refactor-preview

**Usage:** `/refactor-preview [function/class/file]`

Show the blast radius of a function, class, or file before making any changes.

## What it does

Runs the `blast-radius` skill to produce a read-only impact report:
- Direct callers (immediate dependents)
- Indirect callers (2nd-level dependents)
- Test files covering the target
- Public API exposure check

## Example

```
/refactor-preview getUserById
```

```
BLAST RADIUS: getUserById
─────────────────────────────────────
Total files affected: 6

DIRECT (immediate callers): 3 files
  src/services/UserService.ts:42   — import
  src/api/routes.ts:17             — invocation
  src/middleware/auth.ts:33        — invocation

INDIRECT (2nd-level callers): 2 files
  src/controllers/AuthController.ts:88
  src/controllers/AdminController.ts:2

TEST (test coverage): 1 file
  tests/UserService.test.ts:12     — "should fetch user by id"

PUBLIC-API: YES
  Exported from src/index.ts:5

Highest-risk files:
  1. src/services/UserService.ts  (3 callsites)
  2. src/api/routes.ts            (2 callsites)
```

## Next steps

Run `/refactor-plan [change]` to get a safe ordered change checklist.
