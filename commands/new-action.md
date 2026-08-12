# /new-action <ActionType> <EntityName>

Generates a single MDK action file of the specified type.

**ActionType options:** `CreateEntity` | `UpdateEntity` | `DeleteEntity` | `Navigation` | `Message` | `ChangeSet` | `CheckRequiredFields` | `ToastMessage` | `Banner` | `InitializeOfflineOData` | `DownloadOfflineOData` | `UploadOfflineOData`

## Steps

1. Read `.service.metadata` for entity set name and properties.
2. Read related page to get exact control `_Name` values for bindings:
   ```bash
   cat Pages/<EntityName>/<EntityName>_Create.page
   ```
3. Generate the complete `.action` file with `_Type`, `ActionResult`, `Target`, `Properties`, `OnSuccess`, `OnFailure`.
4. For `DeleteEntity`: also generate `ConfirmDelete.action` (Message dialog).
5. List i18n keys to add.
