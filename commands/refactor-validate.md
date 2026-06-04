# /refactor-validate

**Usage:** `/refactor-validate`

Check for stale references after a refactor — verify the job is fully done.

## What it does

Runs the `refactor-validate` skill:
1. Searches for any remaining uses of the OLD name/path
2. Checks for broken imports
3. Verifies test files reference valid (new) symbols
4. Reports exact file+line for any remaining issues

## When to use

Run this after completing all steps from `/refactor-plan`. Re-run until output shows all green.

## Example — clean

```
VALIDATE: getUserById → fetchUserById
──────────────────────────────────────
✓ No stale references found
✓ No broken imports
✓ All test files updated
Refactor complete.
```

## Example — stale refs remain

```
VALIDATE: getUserById → fetchUserById
──────────────────────────────────────
✗ Stale references found (2):

src/middleware/auth.ts:33
  getUserById(req.userId)
  → Fix: rename to fetchUserById(req.userId)

tests/integration/auth.test.ts:77
  mock getUserById
  → Fix: update mock to fetchUserById

✗ Broken imports (1):
  src/controllers/AdminController.ts:2
    import { getUserById } from '../services/UserService'
    → Fix: change import to fetchUserById
```

Fix the listed items, then re-run `/refactor-validate`.
