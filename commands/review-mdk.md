# /review-mdk

Full MDK best practices audit — pages, actions, rules, i18n, offline, deployment readiness.

## Steps

1. Read full project:
   ```bash
   find . -name "*.page" -o -name "*.action" -o -name "*.js" | grep -v node_modules | grep -v .build
   cat .project.json
   cat i18n/i18n.properties
   npx @sap/mdk-tools validate --project . 2>&1
   ```

2. Audit and report ✅ / ❌ / ⚠️ for each item from `mdk-best-practices` skill checklist:
   - Pages: Search enabled, ObjectHeader on detail, ActionBar on create/edit, DataSubscriptions, no hardcoded strings
   - Actions: OnSuccess/OnFailure on all OData, error binding, ModalPage for create/edit, ConfirmDelete before delete
   - Rules: ES6 default export, return value, no async/await, ListPicker return shape
   - i18n: no hardcoded strings, all keys exist, no orphaned keys
   - Offline (if applicable): Upload before CRUD, Download after Upload, ShowActivityIndicator
   - Deployment: .service.metadata exists, validate passes, CF logged in

3. Output structured report — blocking errors first, then warnings, then passing.
