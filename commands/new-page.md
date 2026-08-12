# /new-page <PageType> <EntityName>

Generates a single MDK page of the specified type.

**PageType options:** `ObjectTable` | `FormCell` | `ObjectHeader` | `KeyValue` | `Timeline` | `Calendar` | `DataTable`

## Steps

1. Read `.service.metadata` for entity properties and Edm types.
2. Read existing pages to understand current structure:
   ```bash
   ls Pages/<EntityName>/
   cat i18n/i18n.properties
   ```
3. Generate the complete `.page` file with correct `_Type`, `_Name`, controls, sections, bindings, i18n references.
4. List i18n keys to append to `i18n/i18n.properties`.
