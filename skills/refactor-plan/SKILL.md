# refactor-plan

Generate a safe, ordered refactoring sequence that minimizes intermediate broken state.

## Trigger

Use when: user asks "plan my refactor", "how do I rename X safely", or runs `/refactor-plan [change]`.

## Input

User describes the proposed change as one of:
- **Rename** — `rename getUserById → fetchUserById`
- **Signature change** — `add required param: getUserById(id, options)`
- **Split function** — `split processOrder into validateOrder + fulfillOrder`
- **Move file** — `move src/utils/format.ts → src/helpers/format.ts`

## Steps

1. **Run blast-radius skill first** to get the full affected file list.

2. **Order files safest-first**
   - Priority: leaf files first (files with no callers of their own), then mid-level, then entry points last.
   - Tests always last (they will fail intentionally until the change is complete).

3. **For each affected file, generate a change entry:**
   ```
   FILE: src/services/UserService.ts
     Line 42: import { getUserById } → import { fetchUserById }
     Line 87: getUserById(id) → fetchUserById(id)
   ```

4. **Flag breaking changes for external consumers**
   - If the symbol is PUBLIC-API (from blast-radius report): add a `⚠ BREAKING` marker.
   - Suggest a deprecation shim if the change is a rename:
     ```ts
     // DEPRECATED — remove after v2.0
     export const getUserById = fetchUserById;
     ```

5. **Output checklist** — ordered list of files with exact line edits:
   ```
   REFACTOR PLAN: rename getUserById → fetchUserById
   ─────────────────────────────────────────────────
   ⚠ PUBLIC-API BREAKING CHANGE

   Step 1 — src/utils/helpers.ts
     [ ] Line 5: rename export declaration

   Step 2 — src/services/UserService.ts
     [ ] Line 42: update import
     [ ] Line 87: update call

   Step 3 — src/api/routes.ts
     [ ] Line 17: update call

   Step 4 — src/index.ts
     [ ] Line 3: update re-export
     [ ] Add deprecation shim below

   Step 5 — tests/ (run after all source changes)
     [ ] tests/UserService.test.ts line 12: update mock target name
   ```

6. **Do not modify any files.** This skill generates a plan only. Execution is the user's responsibility.
