# /new-mdk-project

Interactive wizard that scaffolds a complete SAP MDK application from an OData service.

## Steps

1. Read the project folder name — this becomes the app name:
   ```bash
   cat .project.json
   ls Services/
   ```

2. Check if `.service.metadata` exists:
   ```bash
   ls .service.metadata 2>/dev/null && echo "found" || echo "MISSING"
   ```
   If missing, stop and instruct: VS Code → Cmd/Ctrl+Shift+P → **"MDK: Open Mobile App Editor"** → create or select your app → "Add App to Project".

3. Ask the user:
   - Which OData entity sets to scaffold? (comma-separated)
   - Online or offline mode?
   - Template: `crud`, `list-detail`, or `base`?

4. Read `.service.metadata` to confirm entity set names exist.

5. Generate all files using `mdk-create` skill templates.

6. Validate:
   ```bash
   npx @sap/mdk-tools validate --project .
   ```

7. Report created files and next steps.
