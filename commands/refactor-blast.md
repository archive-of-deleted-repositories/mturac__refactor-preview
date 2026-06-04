# /refactor-blast

**Usage:** `/refactor-blast`

Detailed impact breakdown — same as `/refactor-preview` but with extended context: shows the actual code lines at each callsite, not just file+line references.

## What it does

Runs the `blast-radius` skill with verbose output mode:
- Full code context (3 lines around each callsite)
- Annotated zone classification per hit
- Transitive dependency chain visualization

## Example

```
/refactor-blast getUserById
```

Produces the standard blast radius report plus:

```
CALLSITE DETAIL
───────────────
[DIRECT] src/api/routes.ts:17
  15: router.get('/user/:id', async (req, res) => {
  16:   const id = req.params.id;
→ 17:   const user = await getUserById(id);
  18:   res.json(user);

[INDIRECT] src/controllers/AuthController.ts:88
  86: async validateSession(token: string) {
  87:   const payload = decodeToken(token);
→ 88:   return routes.getUserById(payload.sub);   // via routes module
  89: }
```
