# refactor-validate

Verify a refactoring is fully complete — no stale references remain.

## Trigger

Use when: user says "I finished the refactor", "check my refactor", or runs `/refactor-validate`.

## Input

The OLD name/path that was changed (e.g., `getUserById`, `src/utils/format.ts`).

## Steps

1. **Search for remaining references to the OLD name**
   ```bash
   grep -rn "<OLD_NAME>" src/ tests/ --include="*.ts" --include="*.tsx" --include="*.js"
   ```
   If any hits: list them as stale references.

2. **Check for broken imports**
   - If target was a file move: `grep -rn "from.*<OLD_PATH>" . --include="*.ts" --include="*.js"`
   - If target was a rename: `grep -rn "import.*{<OLD_NAME>}" . --include="*.ts" --include="*.js"`
   - Report any file still importing the old symbol or old path.

3. **Check that tests reference valid symbols**
   - `grep -rn "<OLD_NAME>" . --include="*.test.*" --include="*.spec.*"`
   - Each hit is a test that references a now-deleted symbol — report as broken test.

4. **Output validation result**

   **If clean:**
   ```
   VALIDATE: getUserById → fetchUserById
   ──────────────────────────────────────
   ✓ No stale references found
   ✓ No broken imports
   ✓ All test files updated
   Refactor complete.
   ```

   **If stale references exist:**
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

5. **Never auto-fix.** Show findings only. User applies fixes, then re-runs `/refactor-validate`.
