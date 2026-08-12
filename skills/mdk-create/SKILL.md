---
name: mdk-create
version: 0.4.0
description: >
  Use when initializing a new MDK project structure or adding entity metadata
  (pages, actions, i18n) to an existing MDK project. Trigger on: "create an MDK
  app", "generate a new MDK project", "scaffold CRUD for entity X", "add list
  and detail pages for entity Y", "create offline MDK project", "add entity to
  my MDK project", "generate MDK project from OData service". Covers both
  project-scope (full project skeleton) and entity-scope (CRUD or List-Detail
  pages/actions for one or more OData entity sets).
source: SAP/mdk-mcp-server (Apache-2.0) — res/templates/
---

# MDK Project & Entity Scaffolding

You generate MDK project structures and entity metadata from scratch using the
patterns below. No external tools are available — generate all files directly
from these embedded templates and the OData entity information the user provides.

---

## Pre-Generation Checklist

Before generating anything, confirm:
1. **OData entity set names** — ask if not provided.
2. **App name** — used in all file paths (e.g. `MDKApp`).
3. **Service file path** — e.g. `/MDKApp/Services/SampleService.service`.
4. **Online or offline** — default to online unless user says "offline" or mentions field workers.
5. **Scope** — `project` (full new project) or `entity` (add pages to existing project).
6. **Template type** — `crud` (full CRUD), `list-detail` (read-only), or `base` (skeleton only).

**Never generate:**
- `.service.metadata` file
- `.xml` files in `Services/` folder
- Modify `.project.json`

---

## Template: `base` (project scope only)

Generates a minimal MDK project skeleton. Use when no entity sets are known yet,
or user wants a starting shell.

**Files to generate:**

```
MDKApp/
├── Application.app
├── Pages/          (empty)
├── Actions/        (empty)
├── Rules/          (empty)
├── i18n/
│   └── i18n.properties
└── Services/       (empty — user adds .service via VS Code extension)
```

**Application.app:**
```json
{
  "_Type": "Application",
  "_Name": "MDKApp",
  "MainPage": "/MDKApp/Pages/Main.page",
  "OnLaunched": "/MDKApp/Actions/ApplicationOnLaunched.action",
  "Styles": "/MDKApp/Styles/Styles.less"
}
```

---

## Template: `list-detail` (entity scope)

For each entity set, generates a List page and a Detail page (read-only, no create/edit/delete).

### List Page — `{Entity}_List.page`

```json
{
  "Caption": "{i18n>EntityList_Caption}",
  "Controls": [
    {
      "_Name": "SectionedTable0",
      "_Type": "Control.Type.SectionedTable",
      "Sections": [
        {
          "_Name": "ObjectTable0",
          "_Type": "Section.Type.ObjectTable",
          "Header": {
            "Caption": "{i18n>EntityList_Header}",
            "UseTopPadding": false
          },
          "Search": {
            "Enabled": true,
            "Delay": 500,
            "MinimumCharacterThreshold": 3,
            "Placeholder": "{i18n>Search_Placeholder}",
            "BarcodeScanner": false
          },
          "ObjectCell": {
            "Title": "{EntityProperty1}",
            "Subhead": "{EntityProperty2}",
            "Footnote": "{EntityProperty3}",
            "AccessoryType": "DisclosureIndicator",
            "OnPress": "/MDKApp/Actions/Entity/NavToEntity_Detail.action"
          },
          "EmptySection": {
            "Caption": "{i18n>NoItems}"
          },
          "Target": {
            "EntitySet": "EntitySetName",
            "Service": "/MDKApp/Services/SampleService.service",
            "QueryOptions": "$orderby=EntityProperty1 asc"
          }
        }
      ]
    }
  ],
  "_Name": "Entity_List",
  "_Type": "Page"
}
```

### Detail Page — `{Entity}_Detail.page`

```json
{
  "Caption": "{i18n>EntityDetail_Caption}",
  "Controls": [
    {
      "_Name": "SectionedTable0",
      "_Type": "Control.Type.SectionedTable",
      "Sections": [
        {
          "_Name": "ObjectHeaderSection",
          "_Type": "Section.Type.ObjectHeader",
          "ObjectHeader": {
            "HeadlineText": "{EntityProperty1}",
            "Subhead": "{EntityProperty2}",
            "Footnote": "{EntityProperty3}",
            "StatusText": "{EntityStatus}",
            "DetailImage": "sap-icon://product",
            "DetailImageIsCircular": true
          }
        },
        {
          "_Name": "SectionKeyValue0",
          "_Type": "Section.Type.KeyValue",
          "Header": { "Caption": "{i18n>Details_Header}", "UseTopPadding": false },
          "KeyAndValues": [
            { "KeyName": "{i18n>EntityProperty1_Label}", "Value": "{EntityProperty1}" },
            { "KeyName": "{i18n>EntityProperty2_Label}", "Value": "{EntityProperty2}" },
            { "KeyName": "{i18n>EntityProperty3_Label}", "Value": "{EntityProperty3}" }
          ],
          "Layout": { "NumberOfColumns": 2 }
        }
      ],
      "DataSubscriptions": ["EntitySetName"]
    }
  ],
  "_Name": "Entity_Detail",
  "_Type": "Page"
}
```

### Navigation Action — `NavToEntity_Detail.action`

```json
{
  "_Type": "Action.Type.Navigation",
  "PageToOpen": "/MDKApp/Pages/Entity/Entity_Detail.page"
}
```

---

## Template: `crud` (entity scope)

Generates full Create / Read / Update / Delete flow for each entity set.
Includes all `list-detail` files PLUS the following additions:

### Create Page — `{Entity}_Create.page`

```json
{
  "ActionBar": {
    "Items": [
      { "SystemItem": "Cancel", "Position": "Left", "OnPress": "/MDKApp/Actions/CancelPage.action" },
      { "Caption": "{i18n>Create_Button}", "Position": "Right", "OnPress": "/MDKApp/Actions/Entity/Entity_CreateEntity.action" }
    ]
  },
  "Caption": "{i18n>EntityCreate_Caption}",
  "Controls": [
    {
      "_Name": "SectionedTable0",
      "_Type": "Control.Type.SectionedTable",
      "Sections": [
        {
          "_Name": "FormCellSection0",
          "_Type": "Section.Type.FormCell",
          "Controls": [
            {
              "_Name": "EntityProperty1",
              "_Type": "Control.Type.FormCell.SimpleProperty",
              "Caption": "{i18n>EntityProperty1_Label}",
              "IsEditable": true,
              "IsRequired": true,
              "PlaceHolder": "{i18n>EntityProperty1_Placeholder}"
            },
            {
              "_Name": "EntityProperty2",
              "_Type": "Control.Type.FormCell.SimpleProperty",
              "Caption": "{i18n>EntityProperty2_Label}",
              "IsEditable": true
            }
          ]
        }
      ]
    }
  ],
  "_Name": "Entity_Create",
  "_Type": "Page"
}
```

### CreateEntity Action — `Entity_CreateEntity.action`

```json
{
  "_Type": "Action.Type.ODataService.CreateEntity",
  "ActionResult": { "_Name": "entityCreate" },
  "Properties": {
    "EntityProperty1": "#Control:EntityProperty1/#Value",
    "EntityProperty2": "#Control:EntityProperty2/#Value"
  },
  "Target": {
    "EntitySet": "EntitySetName",
    "Service": "/MDKApp/Services/SampleService.service"
  },
  "OnSuccess": "/MDKApp/Actions/Entity/Entity_CreateSuccess.action",
  "OnFailure": "/MDKApp/Actions/Entity/Entity_CreateFailed.action"
}
```

### CreateSuccess Action — `Entity_CreateSuccess.action`

```json
{
  "_Type": "Action.Type.ToastMessage",
  "Message": "{i18n>CreateSuccess_Message}",
  "Duration": 3,
  "Animated": true,
  "OnSuccess": "/MDKApp/Actions/ClosePage.action"
}
```

### CreateFailed Action — `Entity_CreateFailed.action`

```json
{
  "_Type": "Action.Type.ToastMessage",
  "Message": "{{#ActionResults:entityCreate/#Property:error}}",
  "Duration": 5,
  "Animated": true
}
```

### DeleteEntity Action — `Entity_DeleteEntity.action`

```json
{
  "_Type": "Action.Type.ODataService.DeleteEntity",
  "ActionResult": { "_Name": "entityDelete" },
  "Target": {
    "EntitySet": "EntitySetName",
    "ReadLink": "{@odata.readLink}",
    "Service": "/MDKApp/Services/SampleService.service"
  },
  "OnSuccess": "/MDKApp/Actions/Entity/Entity_DeleteSuccess.action",
  "OnFailure": "/MDKApp/Actions/Entity/Entity_DeleteFailed.action"
}
```

### DeleteSuccess Action — `Entity_DeleteSuccess.action`

```json
{
  "_Type": "Action.Type.ToastMessage",
  "Message": "{i18n>DeleteSuccess_Message}",
  "Duration": 3,
  "Animated": true
}
```

### DeleteFailed Action — `Entity_DeleteFailed.action`

```json
{
  "_Type": "Action.Type.ToastMessage",
  "Message": "{{#ActionResults:entityDelete/#Property:error}}",
  "Duration": 5,
  "Animated": true
}
```

### UpdateEntity Action — `Entity_UpdateEntity.action`

```json
{
  "_Type": "Action.Type.ODataService.UpdateEntity",
  "ActionResult": { "_Name": "entityUpdate" },
  "Properties": {
    "EntityProperty1": "#Control:EntityProperty1/#Value",
    "EntityProperty2": "#Control:EntityProperty2/#Value"
  },
  "Target": {
    "EntitySet": "EntitySetName",
    "ReadLink": "{@odata.readLink}",
    "Service": "/MDKApp/Services/SampleService.service"
  },
  "OnSuccess": "/MDKApp/Actions/Entity/Entity_UpdateSuccess.action",
  "OnFailure": "/MDKApp/Actions/Entity/Entity_UpdateFailed.action"
}
```

---

## Offline Mode Additions (when `offline: true`)

Add these to the project when offline mode is requested:

### InitializeOfflineOData.action

```json
{
  "_Type": "Action.Type.OfflineOData.Initialize",
  "Service": "/MDKApp/Services/SampleService.service",
  "ActionResult": { "_Name": "_ODataInitializeResult" },
  "DefiningRequests": [
    { "Name": "EntitySetName", "Query": "EntitySetName" }
  ],
  "ShowActivityIndicator": true,
  "ActivityIndicatorText": "Initializing data. Please wait...",
  "OnSuccess": "/MDKApp/Actions/Service/InitializeODataSuccess.action",
  "OnFailure": "/MDKApp/Actions/Service/InitializeODataFailed.action"
}
```

### DownloadOfflineOData.action

```json
{
  "_Type": "Action.Type.OfflineOData.Download",
  "Service": "/MDKApp/Services/SampleService.service",
  "DefiningRequests": [
    { "Name": "EntitySetName", "Query": "EntitySetName" }
  ],
  "ActionResult": { "_Name": "sync" },
  "OnSuccess": "/MDKApp/Actions/Service/SyncSuccessMessage.action",
  "OnFailure": "/MDKApp/Actions/Service/SyncFailureMessage.action"
}
```

### UploadOfflineOData.action

```json
{
  "_Type": "Action.Type.OfflineOData.Upload",
  "Service": "/MDKApp/Services/SampleService.service",
  "ActionResult": { "_Name": "sync" },
  "OnSuccess": "/MDKApp/Actions/Service/DownloadOfflineOData.action",
  "OnFailure": "/MDKApp/Actions/Service/SyncFailureMessage.action"
}
```

**Offline pattern:** Call `InitializeOfflineOData` on app launch → `DownloadOfflineOData` on
resume/sync → `UploadOfflineOData` before any create/update/delete.

---

## i18n Keys to Generate

Always generate `i18n/i18n.properties` with keys for every label, caption, button,
and message used. Example pattern:

```properties
# Page captions
EntityList_Caption=Entity List
EntityDetail_Caption=Entity Details
EntityCreate_Caption=Create Entity

# Labels
EntityProperty1_Label=Property One
EntityProperty2_Label=Property Two

# Buttons
Create_Button=Create
Save_Button=Save
Cancel_Button=Cancel
Delete_Button=Delete

# Messages
CreateSuccess_Message=Entity created successfully
DeleteSuccess_Message=Entity deleted successfully
NoItems=No items found
Search_Placeholder=Search
Details_Header=Details
```

---

## Key Rules

- Replace `EntitySetName`, `EntityProperty1/2/3`, `MDKApp`, `SampleService` with real values.
- `_Name` must match the filename without extension.
- Control `_Name` values in `#Control:Name/#Value` bindings must exactly match control `_Name`.
- Always add `OnSuccess` and `OnFailure` to every OData action.
- `{@odata.readLink}` is used in Update/Delete Target to reference the current entity.
- For `Edm.Boolean` fields use `Control.Type.FormCell.Switch`.
- For `Edm.DateTime` fields use `Control.Type.FormCell.DatePicker`.
- For all other fields use `Control.Type.FormCell.SimpleProperty`.
- Skip key properties (primary keys) from editable form controls.
