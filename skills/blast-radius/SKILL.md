# blast-radius

Analyze the blast radius of a refactoring target before any code is touched.

## Trigger

Use when: user asks "what would break if I change X", "show blast radius of X", or runs `/refactor-preview`.

## Steps

1. **Accept input** — function name, class name, or file path from user args.

2. **Find direct callers (DIRECT zone)**
   - `grep -rn "import.*{?<TARGET>}?" src/ --include="*.ts" --include="*.tsx" --include="*.js"`
   - `grep -rn "<TARGET>(" src/ --include="*.ts" --include="*.tsx" --include="*.js"`
   - Record: file path, line number, call type (import / invocation).

3. **Find transitive callers (INDIRECT zone)**
   - For each file in DIRECT, repeat step 2 treating that file as the new target (by filename/module path).
   - Limit to 2 levels deep. Do not recurse further.

4. **Find test coverage (TEST zone)**
   - `grep -rn "<TARGET>" . --include="*.test.*" --include="*.spec.*"`
   - Record test file path and test name (line context).

5. **Classify PUBLIC-API zone**
   - Check if target is exported from `index.ts`, `index.js`, or any file in `lib/`.
   - `grep -n "export.*<TARGET>" src/index.ts lib/index.ts 2>/dev/null`
   - If found: mark as PUBLIC-API — breaking this affects external consumers.

6. **Compile blast radius report**
   ```
   BLAST RADIUS: <TARGET>
   ─────────────────────────────────────
   Total files affected: N

   DIRECT (immediate callers): X files
     src/services/UserService.ts:42  — import
     src/api/routes.ts:17            — invocation

   INDIRECT (2nd-level callers): Y files
     src/controllers/AuthController.ts:88

   TEST (test coverage): Z files
     tests/UserService.test.ts:12    — "should create user"

   PUBLIC-API: YES / NO
     Exported from src/index.ts:5

   Highest-risk files (most callsites):
     1. src/services/UserService.ts  (3 callsites)
     2. src/api/routes.ts            (2 callsites)
   ```

7. **Guardrail** — this skill is read-only. Never write, edit, or delete any file. If asked to modify files, redirect to `refactor-plan` skill.

## Output format

Plain text table as shown in step 6. No markdown headers beyond the report block.
