---
name: mdk-docs
version: 0.4.0
description: >
  Use when answering questions about MDK components, properties, schemas, or
  examples — without calling any external tool. Trigger on: "what properties
  does ObjectTable have", "show me the schema for FormCell.SimpleProperty",
  "what is AccessoryType", "what does StatusText do", "how do I use Tags",
  "explain the Target property", "what controls can I use on a detail page",
  "what is the _Type for a list picker", "show example of ObjectHeader",
  "what OData action types are available", "how does DefiningRequests work".
  Also used for MDK best practice questions and guidelines.
source: SAP/mdk-mcp-server (Apache-2.0) — res/schemas/
---

# MDK Documentation & Component Reference

You answer MDK documentation questions using the embedded component reference
below. This covers schema version 26.3 (latest). Always answer from this content
directly — no tool calls needed.

---

## Core MDK Rules (Always Active)

- **Never generate** `.service.metadata` — use VS Code MDK extension.
- **Never generate** `.xml` files in `Services/` folder.
- **Never modify** `.project.json`.
- **Never modify** the project when user is only asking for help/guidance.
- **Always** use `{i18n>Key}` for user-visible strings — never hardcode text.
- **Always** provide `OnSuccess` and `OnFailure` on every OData action.
- **Always** match `_Name` to the filename (without extension).
- **Never** use deprecated properties — check schema version first.

---

## Binding Syntax Reference

| Pattern | Meaning |
|---|---|
| `{PropertyName}` | OData property from current binding context |
| `{@odata.readLink}` | OData read link of current entity |
| `{{#Property:Name}}` | Explicit property reference |
| `{{#ActionResults:resultName/#Property:error}}` | Action result error message |
| `{i18n>KeyName}` | Internationalized string from i18n.properties |
| `#Control:ControlName/#Value` | Value from a named form control |
| `#Control:ListPicker/#SelectedValue` | Selected value from ListPicker |
| `/App/Rules/RuleName.js` | Rule reference — evaluated at runtime |
| `$(N,{Price},'',{minimumFractionDigits:2})` | Number format expression |
| `$(D,{Date},'',{format:'medium'})` | Date format expression |

---

## Page Component Reference

### `Page` (root type)

```
_Type: "Page"
_Name: string          — must match filename without extension
Caption: string        — page title (use i18n binding)
Controls: array        — array of top-level controls
ActionBar: object      — optional action bar with Items array
OnLoaded: string       — optional rule/action called when page loads
```

### `Control.Type.SectionedTable`

Primary container for all list and detail pages. Place as root control.

```
_Type: "Control.Type.SectionedTable"
_Name: string
Sections: array        — array of Section objects
DataSubscriptions: []  — entity set names to watch for updates
```

---

## Section Types Reference

### `Section.Type.ObjectTable`

List of items, each rendered as an ObjectCell. Used for list pages.

```
_Type: "Section.Type.ObjectTable"
_Name: string
Header: { Caption, UseTopPadding }
Footer: { Caption, AttributeLabel, AccessoryType, FooterStyle }
Search: {
  Enabled: boolean
  Delay: number (ms)
  MinimumCharacterThreshold: number
  Placeholder: string
  BarcodeScanner: boolean
}
ObjectCell: {
  Title: string              — primary label (OData binding or i18n)
  Subhead: string            — secondary label
  Footnote: string           — tertiary label
  Description: string        — longer description
  StatusText: string         — right-side status
  SubstatusText: string      — below status
  DetailImage: string        — icon (sap-icon://name) or image URL
  DetailImageIsCircular: boolean
  AccessoryType: "DisclosureIndicator" | "None" | "Checkmark" | "Detail"
  OnPress: string            — action path
  Tags: [{ Text, Color }]   — colored tags ("Green","Red","Blue","Orange","Yellow")
  Icons: [string]            — icon array (sap-icon://name)
  ContextMenu: { Items, LeadingItems, TrailingItems }
}
EmptySection: { Caption: string }
Target: {
  EntitySet: string
  Service: string
  QueryOptions: string       — OData query string e.g. "$top=20&$orderby=Name asc"
}
```

### `Section.Type.ObjectHeader`

Header section for detail pages. Shows entity summary at the top.

```
_Type: "Section.Type.ObjectHeader"
_Name: string
ObjectHeader: {
  HeadlineText: string       — main title
  Subhead: string            — subtitle
  Footnote: string           — below subtitle
  Description: string        — description text
  BodyText: string           — body content
  StatusText: string         — status (top right)
  SubstatusText: string      — below status
  DetailImage: string        — icon or image
  DetailImageIsCircular: boolean
  Tags: [{ Text, Color }]
}
```

### `Section.Type.KeyValue`

Displays key-value pairs in a grid. Used in detail pages for properties.

```
_Type: "Section.Type.KeyValue"
_Name: string
Header: { Caption, UseTopPadding }
KeyAndValues: [
  { KeyName: string, Value: string }
]
Layout: { NumberOfColumns: 1 | 2 | 3 }
MaxItemCount: number
```

### `Section.Type.FormCell`

Container for form input controls. Used in create/edit pages.

```
_Type: "Section.Type.FormCell"
_Name: string
Controls: array            — array of FormCell controls
Header: { Caption }
```

---

## FormCell Control Types

### `Control.Type.FormCell.SimpleProperty`

Single-line text input or read-only display.

```
_Type: "Control.Type.FormCell.SimpleProperty"
_Name: string              — used in #Control:Name/#Value bindings
Caption: string            — field label
Value: string | rule       — initial/display value
IsEditable: boolean
IsRequired: boolean
IsVisible: boolean
PlaceHolder: string
KeyboardType: "Default" | "Phone" | "Number" | "Email" | "URL"
```

### `Control.Type.FormCell.Switch`

Boolean toggle. Use for `Edm.Boolean` OData properties.

```
_Type: "Control.Type.FormCell.Switch"
_Name: string
Caption: string
Value: boolean             — initial value
IsEditable: boolean
```

### `Control.Type.FormCell.DatePicker`

Date/time picker. Use for `Edm.DateTime` OData properties.

```
_Type: "Control.Type.FormCell.DatePicker"
_Name: string
Caption: string
Value: string              — initial value
IsEditable: boolean
Mode: "Date" | "Time" | "DateTime"
```

### `Control.Type.FormCell.ListPicker`

Dropdown/picker from a list of items.

```
_Type: "Control.Type.FormCell.ListPicker"
_Name: string
Caption: string
Value: string | array      — selected value(s)
IsEditable: boolean
AllowMultipleSelection: boolean
Items: rule | array        — [{ ReturnValue, DisplayValue }]
```

List items format (from a rule):
```javascript
return [
  { ReturnValue: 'key1', DisplayValue: 'Display 1' },
  { ReturnValue: 'key2', DisplayValue: 'Display 2' }
];
```

### `Control.Type.FormCell.Note`

Multi-line text area.

```
_Type: "Control.Type.FormCell.Note"
_Name: string
Caption: string
Value: string
IsEditable: boolean
PlaceHolder: string
MaxLength: number
```

---

## Action Type Reference

### OData Actions

| `_Type` | Purpose |
|---|---|
| `Action.Type.ODataService.CreateEntity` | Create new OData entity |
| `Action.Type.ODataService.UpdateEntity` | Update existing entity by ReadLink |
| `Action.Type.ODataService.DeleteEntity` | Delete entity by ReadLink |
| `Action.Type.ODataService.ChangeSet` | Batch multiple OData operations |
| `Action.Type.OfflineOData.Initialize` | Initialize offline store on app start |
| `Action.Type.OfflineOData.Download` | Download/sync data from server |
| `Action.Type.OfflineOData.Upload` | Upload local changes to server |
| `Action.Type.OfflineOData.Close` | Close the offline store |
| `Action.Type.OfflineOData.Clear` | Clear the offline store |

**Common OData Action Properties:**
```
ActionResult: { _Name: string }   — name for referencing result in bindings
Target: {
  EntitySet: string
  Service: string
  ReadLink: "{@odata.readLink}"   — for Update/Delete
}
Properties: { PropertyName: "#Control:ControlName/#Value" }  — for Create/Update
OnSuccess: string                 — action or page to invoke on success
OnFailure: string                 — action or page to invoke on failure
```

### UI Actions

| `_Type` | Purpose |
|---|---|
| `Action.Type.Navigation` | Navigate to a page |
| `Action.Type.Message` | Show alert dialog |
| `Action.Type.ToastMessage` | Show brief toast notification |
| `Action.Type.BannerMessage` | Show banner notification |
| `Action.Type.CheckRequiredFields` | Validate required form fields |
| `Action.Type.Filter` | Open filter panel |
| `Action.Type.PopoverMenu` | Show popover action menu |
| `Action.Type.ClosePage` | Close current page |

**Navigation Action Properties:**
```
PageToOpen: string               — path to .page file
ModalPage: boolean               — open as modal
ModalPageFullscreen: boolean     — full screen modal
```

**Message Action Properties:**
```
Title: string
Message: string
OKCaption: string
CancelCaption: string
OnOK: string
OnCancel: string
```

---

## Target Property

Used in OData read sections and actions:

```json
{
  "Target": {
    "EntitySet": "Products",
    "Service": "/MDKApp/Services/SampleService.service",
    "QueryOptions": "$top=20&$orderby=Name asc&$filter=Status eq 'Active'",
    "ReadLink": "{@odata.readLink}"
  }
}
```

- `QueryOptions` supports standard OData: `$filter`, `$orderby`, `$top`, `$skip`, `$select`, `$expand`
- `ReadLink` uses `{@odata.readLink}` to reference the current binding entity (for Update/Delete)
- For navigation properties: `"EntitySet": "{@odata.readLink}/RelatedEntitySet"`

---

## DataSubscriptions

Add to `SectionedTable` to auto-refresh when these entity sets change:

```json
"DataSubscriptions": ["Products", "PurchaseOrderItems"]
```

---

## ActionBar

Placed at page root level. Common patterns:

```json
"ActionBar": {
  "Items": [
    { "SystemItem": "Cancel", "Position": "Left",  "OnPress": "/MDKApp/Actions/CancelPage.action" },
    { "Caption": "Save",      "Position": "Right", "OnPress": "/MDKApp/Actions/Entity/Save.action" },
    { "Image": "sap-icon://edit", "Position": "Right", "OnPress": "/MDKApp/Actions/Entity/NavToEdit.action" }
  ]
}
```

`SystemItem` values: `Cancel`, `Done`, `Save`, `Edit`, `Add`, `Trash`, `Refresh`, `Search`

---

## File Reference Rules

- Page files: `/AppName/Pages/Folder/PageName.page`
- Action files: `/AppName/Actions/Folder/ActionName.action`
- Rule files: `/AppName/Rules/Folder/RuleName.js`
- Service file: `/AppName/Services/ServiceName.service`
- i18n file: `i18n/i18n.properties`

All file references in metadata must include the `.page`, `.action`, or `.js` extension.
File `_Name` must exactly match the filename without extension.

---

## Offline OData Pattern

Full offline initialization flow:

```
App Launch
  → Action.Type.OfflineOData.Initialize (DefiningRequests for each entity set)
      OnSuccess → DownloadOfflineOData
      OnFailure → Show error message

Before any Create/Update/Delete:
  → Action.Type.OfflineOData.Upload
      OnSuccess → DownloadOfflineOData (refresh)
      OnFailure → Show sync error

Manual sync / pull-to-refresh:
  → Action.Type.OfflineOData.Upload
      OnSuccess → DownloadOfflineOData
```

`DefiningRequests` format:
```json
"DefiningRequests": [
  { "Name": "Products",  "Query": "Products" },
  { "Name": "Customers", "Query": "Customers?$filter=Active eq true" }
]
```

---

## sap-icon Reference (Common Icons)

| Icon | Usage |
|---|---|
| `sap-icon://product` | Products |
| `sap-icon://customer` | Customers |
| `sap-icon://employee` | Employees/Users |
| `sap-icon://edit` | Edit action |
| `sap-icon://delete` | Delete action |
| `sap-icon://add` | Add/create |
| `sap-icon://refresh` | Refresh/sync |
| `sap-icon://search` | Search |
| `sap-icon://filter` | Filter |
| `sap-icon://cart` | Shopping cart |
| `sap-icon://documents` | Documents |
| `sap-icon://alert` | Alert/warning |
| `sap-icon://accept` | Success/checkmark |
| `sap-icon://decline` | Error/decline |
| `sap-icon://synchronize` | Sync |
| `sap-icon://home` | Home |
| `sap-icon://settings` | Settings |

---

## Reference

- MDK Docs: https://help.sap.com/docs/MDK
- MDK Component API: https://help.sap.com/docs/MDK/977416d43cd74bdc958289038749100e/65d2a27ab7e448429e04b9c57cf5a61a.html
- Schema source: SAP/mdk-mcp-server res/schemas/26.3
