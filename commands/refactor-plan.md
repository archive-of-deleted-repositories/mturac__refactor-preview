# /refactor-plan

**Usage:** `/refactor-plan [change]`

Generate a safe, ordered refactoring checklist that minimizes intermediate broken state.

## Supported change types

- `rename getUserById → fetchUserById`
- `signature getUserById(id, options: Options)`
- `split processOrder → validateOrder + fulfillOrder`
- `move src/utils/format.ts → src/helpers/format.ts`

## What it does

Runs the `refactor-plan` skill:
1. Calls blast-radius internally to get all affected files
2. Orders files safest-first (leaves before entry points, tests last)
3. Outputs exact line-level changes per file as a checkbox list

## Example

```
/refactor-plan rename getUserById → fetchUserById
```

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
  [ ] Add deprecation shim below export

Step 5 — tests/ (run after all source changes)
  [ ] tests/UserService.test.ts line 12: update mock
```

## Next steps

After applying all changes, run `/refactor-validate` to confirm no stale references remain.
