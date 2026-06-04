# refactor-preview

Simulate refactoring impact with blast radius analysis before applying changes — see exactly what would break.

## Install

```bash
claude plugin install mturac/refactor-preview
```

## Commands

| Command | What it does |
|---|---|
| `/refactor-preview [target]` | Show blast radius for a function, class, or file |
| `/refactor-blast [target]` | Detailed impact with callsite code context |
| `/refactor-plan [change]` | Generate ordered change checklist |
| `/refactor-validate` | Check for stale references after refactor |

## Example blast radius output

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

## Workflow

1. `/refactor-preview getUserById` — see what would break
2. `/refactor-plan rename getUserById → fetchUserById` — get ordered checklist
3. Apply changes file by file
4. `/refactor-validate` — confirm no stale references remain

## Skills

- `blast-radius` — read-only impact analysis (never modifies files)
- `refactor-plan` — ordered change checklist with exact line edits
- `refactor-validate` — post-refactor completeness check
