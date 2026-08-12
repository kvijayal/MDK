# /new-entity <EntityName>

Scaffolds full CRUD pages, actions, and i18n for a new OData entity.

## Steps

1. Read project context:
   ```bash
   cat .project.json
   cat .service.metadata
   cat i18n/i18n.properties
   ```

2. Confirm `<EntityName>` exists in `.service.metadata` and collect property Edm types.

3. Generate all files per `mdk-create` skill:
   - `Pages/<EntityName>/<EntityName>_List.page`
   - `Pages/<EntityName>/<EntityName>_Detail.page`
   - `Pages/<EntityName>/<EntityName>_Create.page`
   - `Pages/<EntityName>/<EntityName>_Edit.page`
   - All actions: NavTo, CreateEntity, UpdateEntity, DeleteEntity, ConfirmDelete, success/failure toasts
   - Append i18n keys to `i18n/i18n.properties`

4. Validate:
   ```bash
   npx @sap/mdk-tools validate --project .
   ```

5. Remind to wire navigation from app menu or home page to `<EntityName>_List.page`.
