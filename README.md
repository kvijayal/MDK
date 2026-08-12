# SAP MDK AI Skills

> AI skills for SAP Mobile Development Kit (MDK) — installable via the
> [SAP AI Skills Library](https://skills.cloud.sap).

Converted from [SAP/mdk-mcp-server](https://github.com/SAP/mdk-mcp-server) (Apache-2.0).
These skills teach AI coding agents MDK metadata conventions, artifact generation,
project operations, and component documentation — with no MCP server required.

---

## Skills

| Slug | What it does | Triggers on |
|---|---|---|
| `mdk-create` | New project init, CRUD / List-Detail / Base templates, offline mode | "create MDK project", "scaffold CRUD", "add list detail pages", "generate offline MDK project" |
| `mdk-gen` | Individual pages, actions, i18n, JavaScript rules | "generate FormCell page", "create Navigation action", "generate i18n file", "add a rule for" |
| `mdk-manage` | Validate, build, deploy, migrate, QR code — exact `mdkcli` commands | "validate MDK project", "build and deploy", "deploy to Mobile Services", "migrate to schema", "show QR code" |
| `mdk-docs` | Component schemas, property reference, binding syntax, best practices | "what properties does X have", "what is _Type", "binding syntax", "offline pattern", "show example of" |

---

## Install

```bash
# All 4 skills at once
npx skills add <your-org>/mdk-ai-skills

# One at a time
npx skills add <your-org>/mdk-ai-skills --skill mdk-create
npx skills add <your-org>/mdk-ai-skills --skill mdk-gen
npx skills add <your-org>/mdk-ai-skills --skill mdk-manage
npx skills add <your-org>/mdk-ai-skills --skill mdk-docs

# List available skills
npx skills add <your-org>/mdk-ai-skills --list
```

---

## Skill Details

### mdk-create — Project & Entity Scaffolding

Generates complete MDK project structures and entity metadata from embedded
templates (`res/templates/`).

**Templates:**

| Template | Scope | Output |
|---|---|---|
| `base` | project | Minimal skeleton — `Application.app`, empty `Pages/`, `Actions/`, `Rules/`, `i18n/` |
| `list-detail` | project or entity | List page + Detail page (read-only) per entity set |
| `crud` | project or entity | Full Create / Read / Update / Delete pages, actions, and i18n per entity set |

**Offline support:** Adds `InitializeOfflineOData`, `DownloadOfflineOData`,
`UploadOfflineOData` actions and `DefiningRequests` when offline mode is requested.

---

### mdk-gen — Individual Artifact Generation

Generates individual MDK pages, actions, i18n files, and JavaScript rules from
embedded templates (`res/templates/Action/`, `res/templates/Page/`, `res/templates/Rule/`).

**Page types:**
`ObjectTable` | `ObjectHeader` | `FormCell` | `KeyValue` |
`Tabs` | `BottomNavigation` | `Timeline` | `Calendar` | `DataTable`

**Action types:**
`Navigation` | `Message` | `ToastMessage` | `Banner` |
`ODataCreateEntity` | `ODataUpdateEntity` | `ODataDeleteEntity` |
`ChangeSet` | `CheckRequiredFields` | `Filter` | `PopoverMenu` |
`InitializeOfflineOData` | `DownloadOfflineOData` | `UploadOfflineOData`

**Rules:**
ListPicker items | Entity count | Visibility | Status color | OData read | Page OnLoaded

---

### mdk-manage — Project Lifecycle Operations

Provides exact `mdkcli` terminal commands for all MDK project operations.

**Operations:**

| Operation | Command |
|---|---|
| Validate | `npx @sap/mdk-tools validate --project .` |
| Build | `npx @sap/mdk-tools build --target zip --project .` |
| Deploy | `npx @sap/mdk-tools deploy --target mobile --showqr --project .` |
| Migrate | `npx @sap/mdk-tools migrate --project .` |
| QR code | Generated at `.build/qrcode.png` during deploy |

**Pipeline:** `validate → build → deploy → qr code` — always in this order.

---

### mdk-docs — Component Reference & Documentation

Embeds MDK component schemas, property reference, binding syntax, and best
practices from the MDK schema source (`res/schemas/26.3/`). No tool calls needed.

**Covers:**
Page / SectionedTable / ObjectTable / ObjectHeader / KeyValue / FormCell (all variants) /
all action `_Type` values / binding syntax / Target property / offline pattern /
ActionBar / file path conventions / sap-icon reference / always-active MDK rules.

---

## Requirements

| Tool | Purpose | Install |
|---|---|---|
| `@sap/mdk-tools` | MDK CLI for all project operations | `npm i -g @sap/mdk-tools` |
| Cloud Foundry CLI | Required for deploy | [docs.cloudfoundry.org](https://docs.cloudfoundry.org/cf-cli/) |
| VS Code MDK Extension | Required to generate `.service.metadata` | `SAPSE.vsc-extension-mdk` |

---

## Supported Agents

Works with any agent that supports the
[skills CLI](https://github.com/vercel-labs/skills):
Claude Code, Codex, Cursor, OpenCode, and others.

---

## License

Apache-2.0 — converted from [SAP/mdk-mcp-server](https://github.com/SAP/mdk-mcp-server).
