---
name: mdk-gen
version: 0.4.0
description: >
  Use when generating individual MDK artifacts: specific page types (ObjectTable,
  FormCell, ObjectHeader, KeyValue, Calendar, Timeline, DataTable, ObjectCard,
  KPIHeader, ProfileHeader, ObjectCollection, SimplePropertyCollection,
  TimelinePreview, ContactTable), specific action types (CreateODataEntity,
  UpdateODataEntity, DeleteODataEntity, Navigation, Message, DownloadOfflineOData,
  UploadOfflineOData, InitializeOfflineOData, ToastMessage, Banner, Filter,
  ChangeSet, CheckRequiredFields, PopoverMenu, and more), i18n translation files,
  or JavaScript rule examples. Trigger on: "generate a FormCell page", "create a
  Navigation action", "add an ObjectTable section", "generate i18n file",
  "show me a rule for list picker items", "create a filter action".
source: SAP/mdk-mcp-server (Apache-2.0) — res/templates/
---

# MDK Artifact Generator

You generate individual MDK artifact files — pages, actions, i18n, and rules —
using the embedded templates below. Substitute real app name, service path, and
entity set names from the user's project context.

---

## Page Artifact: DataBinding Pages

### ObjectTable Page

Complete list page with search, context menu, and detail navigation:

```json
{
  "Caption": "{i18n>Products_List_Caption}",
  "Controls": [
    {
      "_Name": "SectionedTable0",
      "_Type": "Control.Type.SectionedTable",
      "Sections": [
        {
          "_Name": "ObjectTable0",
          "_Type": "Section.Type.ObjectTable",
          "Header": {
            "Caption": "{i18n>Products_Header}",
            "UseTopPadding": false
          },
          "Search": {
            "Enabled": true,
            "Delay": 500,
            "MinimumCharacterThreshold": 3,
            "Placeholder": "{i18n>Search_Placeholder}",
            "BarcodeScanner": true
          },
          "ObjectCell": {
            "Title": "{Name}",
            "Subhead": "{ProductID}",
            "Footnote": "{Category}",
            "DetailImage": "sap-icon://product",
            "DetailImageIsCircular": true,
            "AccessoryType": "DisclosureIndicator",
            "StatusText": "$(N,{Price},'',{minimumFractionDigits:2,useGrouping:true})",
            "SubstatusText": "{CurrencyCode}",
            "OnPress": "/MDKApp/Actions/NavToProductDetails.action",
            "Tags": [
              { "Text": "In Store", "Color": "Green" },
              { "Text": "On Sale", "Color": "Red" }
            ],
            "ContextMenu": {
              "Items": [
                {
                  "_Name": "delete",
                  "Image": "sap-icon://delete",
                  "Text": "Delete",
                  "Mode": "Destructive",
                  "OnSwipe": "/MDKApp/Actions/Products/DeleteEntity.action"
                }
              ],
              "TrailingItems": ["delete"]
            }
          },
          "Footer": {
            "_Name": "ObjectTableFooter",
            "UseTopPadding": false,
            "AttributeLabel": "/MDKApp/Rules/Products_Count.js"
          },
          "EmptySection": { "Caption": "{i18n>NoItems}" },
          "Target": {
            "EntitySet": "Products",
            "QueryOptions": "$top=20&$orderby=ProductID asc",
            "Service": "/MDKApp/Services/SampleService.service"
          }
        }
      ]
    }
  ],
  "_Name": "Products_List",
  "_Type": "Page"
}
```

### ObjectHeader Page (Detail Page)

```json
{
  "Caption": "{i18n>ProductDetail_Caption}",
  "Controls": [
    {
      "_Name": "SectionedTable0",
      "_Type": "Control.Type.SectionedTable",
      "Sections": [
        {
          "_Name": "ObjectHeaderSection",
          "_Type": "Section.Type.ObjectHeader",
          "ObjectHeader": {
            "HeadlineText": "{Name}",
            "Subhead": "{ProductID}",
            "Footnote": "{Category}",
            "DetailImage": "sap-icon://product",
            "DetailImageIsCircular": true,
            "Description": "{ShortDescription}",
            "StatusText": "$(N,{Price},'',{minimumFractionDigits:2,useGrouping:true})",
            "SubstatusText": "{CurrencyCode}",
            "BodyText": "Dimension {DimensionWidth} x {DimensionDepth} x {DimensionHeight} {DimensionUnit}",
            "Tags": [
              { "Text": "In Store", "Color": "Green" },
              { "Text": "On Sale", "Color": "Red" }
            ]
          }
        },
        {
          "_Name": "SectionKeyValue0",
          "_Type": "Section.Type.KeyValue",
          "Header": { "Caption": "{i18n>Dimensions_Header}", "UseTopPadding": false },
          "KeyAndValues": [
            { "KeyName": "Height", "Value": "{DimensionHeight}" },
            { "KeyName": "Width",  "Value": "{DimensionWidth}" },
            { "KeyName": "Depth",  "Value": "{DimensionDepth}" },
            { "KeyName": "Weight", "Value": "{{#Property:Weight}} {{#Property:WeightUnit}}" }
          ],
          "Layout": { "NumberOfColumns": 2 }
        },
        {
          "_Name": "RelatedItems",
          "_Type": "Section.Type.ObjectTable",
          "Header": { "Caption": "{i18n>RelatedItems_Header}" },
          "ObjectCell": {
            "Title": "{PurchaseOrderID}",
            "Subhead": "{ItemNumber}",
            "StatusText": "{NetAmount}",
            "SubstatusText": "{CurrencyCode}",
            "AccessoryType": "DisclosureIndicator"
          },
          "EmptySection": { "Caption": "{i18n>NoItems}" },
          "Target": {
            "EntitySet": "{@odata.readLink}/PurchaseOrderItems",
            "Service": "/MDKApp/Services/SampleService.service"
          }
        }
      ],
      "DataSubscriptions": ["Products", "PurchaseOrderItems"]
    }
  ],
  "_Name": "Products_Detail",
  "_Type": "Page"
}
```

### FormCell Page (Create/Edit)

```json
{
  "ActionBar": {
    "Items": [
      { "SystemItem": "Cancel", "Position": "Left",  "OnPress": "/MDKApp/Actions/CancelPage.action" },
      { "Caption": "{i18n>Save_Button}",  "Position": "Right", "OnPress": "/MDKApp/Actions/Entity/Entity_Save.action" }
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
              "_Name": "EntityName",
              "_Type": "Control.Type.FormCell.SimpleProperty",
              "Caption": "{i18n>Name_Label}",
              "IsEditable": true,
              "IsRequired": true,
              "PlaceHolder": "{i18n>Name_Placeholder}"
            },
            {
              "_Name": "EntityStatus",
              "_Type": "Control.Type.FormCell.ListPicker",
              "Caption": "{i18n>Status_Label}",
              "IsEditable": true,
              "AllowMultipleSelection": false,
              "Items": "/MDKApp/Rules/GetStatusItems.js"
            },
            {
              "_Name": "EntityEnabled",
              "_Type": "Control.Type.FormCell.Switch",
              "Caption": "{i18n>Enabled_Label}",
              "IsEditable": true,
              "Value": true
            },
            {
              "_Name": "EntityDate",
              "_Type": "Control.Type.FormCell.DatePicker",
              "Caption": "{i18n>Date_Label}",
              "IsEditable": true,
              "Mode": "Date"
            },
            {
              "_Name": "EntityNote",
              "_Type": "Control.Type.FormCell.Note",
              "Caption": "{i18n>Note_Label}",
              "IsEditable": true,
              "PlaceHolder": "{i18n>Note_Placeholder}"
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

---

## Page Artifact: Layout Pages

### Tabs Layout

```json
{
  "Caption": "{i18n>Main_Caption}",
  "Controls": [
    {
      "_Name": "TabControl0",
      "_Type": "Control.Type.TabControl",
      "Items": "/MDKApp/Rules/TabControl/TabItems.js",
      "OnTabSwitch": "/MDKApp/Rules/TabControl/TabControlRule.js"
    }
  ],
  "_Name": "Main",
  "_Type": "Page"
}
```

### BottomNavigation Layout

```json
{
  "Caption": "{i18n>Main_Caption}",
  "Controls": [
    {
      "_Name": "BottomNav0",
      "_Type": "Control.Type.BottomNavigation",
      "Items": "/MDKApp/Rules/BottomNav/NavItems.js"
    }
  ],
  "_Name": "Main",
  "_Type": "Page"
}
```

---

## Action Artifacts

### Navigation Action

```json
{
  "_Type": "Action.Type.Navigation",
  "PageToOpen": "/MDKApp/Pages/Entity/Entity_Detail.page"
}
```

Modal (full screen):
```json
{
  "_Type": "Action.Type.Navigation",
  "PageToOpen": "/MDKApp/Pages/Entity/Entity_Create.page",
  "ModalPage": true,
  "ModalPageFullscreen": true
}
```

### Message Action (Confirmation Dialog)

```json
{
  "_Type": "Action.Type.Message",
  "Title": "{i18n>Delete_Title}",
  "Message": "{i18n>Delete_Confirmation}",
  "OKCaption": "{i18n>Delete_OK}",
  "CancelCaption": "{i18n>Cancel}",
  "OnOK": "/MDKApp/Actions/Entity/Entity_DeleteEntity.action",
  "OnCancel": "/MDKApp/Actions/CancelPage.action"
}
```

### ToastMessage Action

```json
{
  "_Type": "Action.Type.ToastMessage",
  "Message": "{i18n>Success_Message}",
  "Duration": 3,
  "Animated": true,
  "NumberOfLines": 2
}
```

### Banner Action

```json
{
  "_Type": "Action.Type.BannerMessage",
  "Message": "{i18n>Sync_Failed_Message}",
  "Duration": 7,
  "Animated": true
}
```

### CheckRequiredFields Action

```json
{
  "_Type": "Action.Type.CheckRequiredFields",
  "PageToCheck": "#Page:Entity_Create",
  "OnSuccess": "/MDKApp/Actions/Entity/Entity_CreateEntity.action",
  "OnFailure": "/MDKApp/Actions/RequiredFieldsValidationFailed.action"
}
```

### ChangeSet Action (Batch)

```json
{
  "_Type": "Action.Type.ODataService.ChangeSet",
  "Target": {
    "Service": "/MDKApp/Services/SampleService.service"
  },
  "Actions": [
    "/MDKApp/Actions/Entity/Entity_CreateEntity.action",
    "/MDKApp/Actions/Related/Related_CreateEntity.action"
  ],
  "OnSuccess": "/MDKApp/Actions/ChangeSetSuccess.action",
  "OnFailure": "/MDKApp/Actions/ChangeSetFailed.action"
}
```

### Filter Action

```json
{
  "_Type": "Action.Type.Filter",
  "FilterItems": "/MDKApp/Rules/FilterQueries/DefaultFilters.js",
  "OnApply": "/MDKApp/Rules/FilterQueries/SaveFilters.js",
  "OnReset": "/MDKApp/Rules/FilterQueries/ResetFilters.js"
}
```

### PopoverMenu Action

```json
{
  "_Type": "Action.Type.PopoverMenu",
  "Items": [
    {
      "Caption": "{i18n>Edit_Label}",
      "OnPress": "/MDKApp/Actions/Entity/NavToEdit.action",
      "Image": "sap-icon://edit"
    },
    {
      "Caption": "{i18n>Delete_Label}",
      "OnPress": "/MDKApp/Actions/Entity/ConfirmDelete.action",
      "Image": "sap-icon://delete"
    }
  ]
}
```

---

## i18n Artifact

Generate `i18n/i18n.properties` for the current project. Read existing file content first if available, then add missing keys. Format:

```properties
# Page Captions
Products_List_Caption=Products
Products_Detail_Caption=Product Details
ProductCreate_Caption=Create Product
ProductEdit_Caption=Edit Product

# Section Headers
Products_Header=Products
Dimensions_Header=Dimensions & Weight
RelatedItems_Header=Related Items

# Field Labels
Name_Label=Name
Status_Label=Status
Enabled_Label=Active
Date_Label=Date
Note_Label=Notes
Price_Label=Price
Category_Label=Category

# Placeholders
Name_Placeholder=Enter name
Note_Placeholder=Enter notes

# Buttons
Save_Button=Save
Cancel_Button=Cancel
Create_Button=Create
Edit_Button=Edit
Delete_Button=Delete
Sync_Button=Sync

# Messages
CreateSuccess_Message=Record created successfully
UpdateSuccess_Message=Record updated successfully
DeleteSuccess_Message=Record deleted successfully
Sync_Failed_Message=Sync failed

# Dialog
Delete_Title=Confirm Delete
Delete_Confirmation=Are you sure you want to delete this record?
Delete_OK=Delete

# Common
Search_Placeholder=Search
NoItems=No items found
```

---

## Rule Artifacts

Rules are ES module `.js` files receiving a `clientAPI` parameter.

### Get List Picker Items

```javascript
export default function GetStatusItems(clientAPI) {
  return [
    { ReturnValue: 'Open',       DisplayValue: 'Open' },
    { ReturnValue: 'InProgress', DisplayValue: 'In Progress' },
    { ReturnValue: 'Closed',     DisplayValue: 'Closed' }
  ];
}
```

### Entity Count (for list footer)

```javascript
export default function ProductsCount(clientAPI) {
  const serviceName = '/MDKApp/Services/SampleService.service';
  const entitySet = 'Products';
  return clientAPI.count(serviceName, entitySet, '').then((result) => {
    return result > 0 ? result : 0;
  }).catch(() => 0);
}
```

### Get App Name

```javascript
export default function GetAppName(clientAPI) {
  return clientAPI.getAppName();
}
```

### Visibility Rule (boolean)

```javascript
export default function IsEditVisible(clientAPI) {
  const status = clientAPI.binding.Status;
  return status !== 'Closed';
}
```

### Status Color Rule

```javascript
export default function StatusColor(clientAPI) {
  const status = clientAPI.binding.Status;
  if (status === 'Open')       return '#107E3E'; // Green
  if (status === 'InProgress') return '#E9730C'; // Orange
  return '#BB0000';                              // Red
}
```

### Read OData and Return Value

```javascript
export default function GetMaxId(clientAPI) {
  const serviceName = '/MDKApp/Services/SampleService.service';
  const entitySet = 'EntitySetName';
  const queryOptions = '$select=Id&$orderby=Id desc&$top=1';
  return clientAPI.read(serviceName, entitySet, [], queryOptions).then((result) => {
    if (result && result.length > 0) {
      return String(Number(result.getItem(0).Id) + 1);
    }
    return '1';
  });
}
```

### OnLoaded Page Rule

```javascript
export default function PageOnLoaded(clientAPI) {
  const pageProxy = clientAPI.getPageProxy();
  // Set initial values, visibility, or load data here
  return Promise.resolve();
}
```

---

## Binding Reference

| Pattern | Use |
|---|---|
| `{PropertyName}` | OData entity property (current binding context) |
| `{@odata.readLink}` | OData read link of current entity (for update/delete) |
| `{{#Property:Name}}` | Explicit property reference |
| `{{#ActionResults:name/#Property:error}}` | Action result property |
| `{i18n>KeyName}` | i18n string |
| `#Control:ControlName/#Value` | Form control value (in action Properties) |
| `#Control:ListPicker/#SelectedValue` | List picker selected value |
| `/MDKApp/Rules/RuleName.js` | Rule reference (returns computed value) |
| `$(N,{Price},'',{minimumFractionDigits:2})` | Number format expression |
