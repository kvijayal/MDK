# /migrate-mdk

Migrates MDK project metadata to the latest schema version.

## Steps

1. Read current schema version:
   ```bash
   cat .project.json
   ```

2. Show migration path to 26.6: `current → ... → 26.6`
   (valid path: 24.7 → 24.11 → 25.6 → 25.9 → 26.3 → 26.6)

3. Migrate:
   ```bash
   npx @sap/mdk-tools migrate --project .
   ```

4. Validate after migration:
   ```bash
   npx @sap/mdk-tools validate --project .
   ```

5. Report what changed and any items needing manual review.
