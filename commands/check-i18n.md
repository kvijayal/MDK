# /check-i18n

Finds hardcoded strings in MDK metadata and replaces with i18n keys.

## Steps

1. Scan for hardcoded strings:
   ```bash
   grep -rn '"Caption"\s*:\s*"[^{]' Pages/ Actions/ 2>/dev/null
   grep -rn '"Message"\s*:\s*"[^{]' Actions/ 2>/dev/null
   grep -rn '"Title"\s*:\s*"[^{]' Pages/ Actions/ 2>/dev/null
   grep -rn '"Placeholder"\s*:\s*"[^{]' Pages/ 2>/dev/null
   ```

2. Read existing i18n:
   ```bash
   cat i18n/i18n.properties
   ```

3. For each hardcoded string: generate a key, replace with `{i18n>Key}`, add to `i18n.properties`.

4. Validate:
   ```bash
   npx @sap/mdk-tools validate --project .
   ```

5. Report: strings found, replaced, keys added.
