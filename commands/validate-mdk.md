# /validate-mdk

Runs MDK schema validation and reports all errors and warnings.

## Steps

1. Confirm project root:
   ```bash
   cat .project.json
   ```

2. Run validation:
   ```bash
   npx @sap/mdk-tools validate --project .
   ```

3. Display results:
   - ❌ Errors (blocking) — must fix before build/deploy
   - ⚠️ Warnings (non-blocking) — review recommended

4. For each error: file path, what it means, exact fix.

5. If 0 errors: confirm ready to `/build-mdk`.
