---
name: mdk-manage
version: 0.4.0
description: >
  Use when running MDK project lifecycle operations — validating the project
  schema, building the project bundle, deploying to SAP Mobile Services, migrating
  to a newer schema version, showing a QR code, or opening the Mobile App Editor.
  Trigger on: "validate my MDK project", "build and deploy", "deploy the app to
  Mobile Services", "migrate to schema 26.3", "show QR code", "check for errors",
  "how do I open the Mobile App Editor", "generate onboarding QR code".
source: SAP/mdk-mcp-server (Apache-2.0)
---

# MDK Project Operations

You guide users through MDK project lifecycle operations. Since MCP tools are not
available in this environment, provide the exact terminal commands for each operation
and explain what to expect. All operations use the `@sap/mdk-tools` CLI (`mdkcli`).

---

## Prerequisites Check

Before any operation, confirm these are in place:

| Requirement | Check command |
|---|---|
| Node.js ≥ 22 | `node --version` |
| `@sap/mdk-tools` installed | `npm list -g @sap/mdk-tools` |
| Cloud Foundry CLI (deploy only) | `cf --version` |
| CF logged in (deploy only) | `cf target` |
| `.service.metadata` exists (deploy only) | `ls <project>/.service.metadata` |

Install MDK tools if missing:
```bash
npm install -g @sap/mdk-tools
```

---

## Operation: `validate`

Validates all MDK metadata JSON files against the project's schema version.
Reports errors (blocking) and warnings (non-blocking) with file paths.

```bash
# Run from terminal (MCP timeout may occur on large projects — run directly)
mdkcli validate --project "/path/to/your/MDKProject"

# Or navigate first
cd "/path/to/your/MDKProject"
mdkcli validate --project .
```

**Expected output:**
- ✅ `Validation completed. 0 errors, 0 warnings.` → safe to build
- ❌ Errors listed with file path and line → fix before building
- ⚠️ Warnings → review, not blocking

**Common errors and fixes:**

| Error message | Cause | Fix |
|---|---|---|
| `Unknown property 'X'` | Property doesn't exist in this schema version | Check the correct property name in the component schema |
| `Missing required property 'X'` | Mandatory field omitted | Add the required property |
| `Invalid enum value 'X'` | Wrong value for an enum property | Use an allowed enum value |
| `File not found: /path/to/file` | Broken action/page reference | Verify the referenced file exists at that path |
| `_Type is required` | `_Type` missing from a control/action | Add the `_Type` property |

**Schema versions:** 26.3 (default/latest), 25.9, 25.6, 24.11, 24.7

---

## Operation: `build`

Bundles the MDK metadata project using webpack into a deployable `.zip` in `.build/`.

```bash
mdkcli build --target zip --project "/path/to/your/MDKProject"
```

**Prerequisites:** Project must pass `validate` with 0 errors.

**Output:** `.build/MDKProject.zip` (used by deploy).

---

## Operation: `deploy`

Deploys the project to SAP Mobile Services. Triggers build automatically if needed.

```bash
# Standard deploy (also generates QR code)
mdkcli deploy --target mobile \
  --name "your-mobile-app-id" \
  --showqr \
  --project "/path/to/your/MDKProject"

# With external npm packages (e.g., geolocation plugin)
mdkcli deploy --target mobile \
  --name "your-mobile-app-id" \
  --showqr \
  --externals "@nativescript/geolocation" \
  --project "/path/to/your/MDKProject"

# For CAP projects (first-time deploy creates the app in Mobile Services)
mdkcli deploy --target mobile \
  --name "your-mobile-app-id" \
  --showqr \
  --create \
  --destination "DestinationName" \
  --project "/path/to/your/MDKProject"
```

**CF Login (required before deploy):**

```bash
# SSO login (recommended for SAP BTP)
cf login -a https://api.cf.<region>.hana.ondemand.com --sso

# Common BTP CF endpoints:
# EU (Frankfurt):  https://api.cf.eu10.hana.ondemand.com
# US (East):       https://api.cf.us10.hana.ondemand.com
# AP (Tokyo):      https://api.cf.jp10.hana.ondemand.com
# AP (Singapore):  https://api.cf.ap10.hana.ondemand.com

# Verify current target
cf target
```

**If deploy fails:**

| Error | Cause | Fix |
|---|---|---|
| `Not logged in` / `CF token error` | CF session expired | Run `cf login --sso` again |
| `App not found` | App ID in `.service.metadata` doesn't match | Regenerate `.service.metadata` via VS Code MDK extension |
| `Quota exceeded` | BTP subaccount resource limit reached | Check BTP cockpit resource quotas |
| `Build failed` | Project has schema errors | Run `validate` first and fix all errors |

---

## Operation: `migrate`

Migrates project metadata from an older schema version to a newer one. Updates
deprecated properties, renames changed fields, removes removed metadata.

```bash
mdkcli migrate --project "/path/to/your/MDKProject"
```

**After migration:** Always run `validate` to confirm the migrated project is clean.

**Schema version upgrade path (always go forward, never skip):**
```
24.7 → 24.11 → 25.6 → 25.9 → 26.3
```

The current schema version is in `.project.json` → `SchemaVersion`.

---

## Operation: `show-qrcode`

The QR code is generated during deploy and stored at `.build/qrcode.png`.

**To view:**
1. Open VS Code Explorer sidebar.
2. Navigate to `.build/qrcode.png`.
3. Click to open and preview.

**To regenerate** without a full redeploy, run deploy again with `--showqr`.

Users scan this QR code with the **SAP Mobile Services Client** app to register
their device and download the MDK application.

---

## Operation: `open-mobile-app-editor`

The Mobile App Editor is a VS Code extension tool used to create the
`.service.metadata` file. This is required before `deploy`.

**Steps:**
1. Run `cf login --sso` in a terminal to authenticate with CF.
2. In VS Code, press `Cmd+Shift+P` (Mac) or `Ctrl+Shift+P` (Windows/Linux).
3. Select **"MDK: Open Mobile App Editor"**.
4. Create a new mobile app or select an existing one.
5. Select a destination.
6. Click **"Add App to Project"**.

This writes `.service.metadata` to your project root.

**Install the VS Code extension if missing:**
- Extension ID: `SAPSE.vsc-extension-mdk`
- Marketplace: https://marketplace.visualstudio.com/items?itemName=SAPSE.vsc-extension-mdk

---

## Standard Deployment Pipeline

Run in this sequence:

```bash
# 1. Validate — fix all errors before proceeding
mdkcli validate --project "/path/to/MDKProject"

# 2. Build — produces .build/MDKProject.zip
mdkcli build --target zip --project "/path/to/MDKProject"

# 3. Deploy — pushes to Mobile Services + shows QR code
mdkcli deploy --target mobile --name "app-id" --showqr --project "/path/to/MDKProject"

# 4. QR code is at .build/qrcode.png — open in VS Code to scan
```

Stop at any step if it fails. Do not proceed to deploy with validation errors.

---

## CAP Project Notes

If the MDK app is inside a CAP project (`app/<name>_mdk/`), the project path
to pass to all commands is the **MDK sub-folder**, not the CAP root:

```bash
mdkcli validate --project "/path/to/cap-project/app/myapp_mdk"
```

Deploy for CAP projects requires `--create` on first deploy and a `--destination` flag.

---

## Reference

- MDK CLI: `npm info @sap/mdk-tools`
- SAP Mobile Services: https://help.sap.com/docs/SAP_MOBILE_SERVICES
- Cloud Foundry CLI: https://docs.cloudfoundry.org/cf-cli/
- MDK Documentation: https://help.sap.com/docs/MDK
