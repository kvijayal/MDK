# /deploy-mdk

Full pipeline: Validate → Build → Deploy to SAP Mobile Services → QR code.

## Steps

1. Check prerequisites:
   ```bash
   cf --version
   cf target
   ls .service.metadata
   ```
   If any fail, instruct the user how to fix before continuing.

2. Validate:
   ```bash
   npx @sap/mdk-tools validate --project .
   ```

3. Build:
   ```bash
   npx @sap/mdk-tools build --target zip --project .
   ```

4. Deploy:
   ```bash
   npx @sap/mdk-tools deploy --target mobile --showqr --project .
   ```

5. QR code saved at `.build/qrcode.png` — open in VS Code Explorer to scan.

6. Report deployment status and Mobile Services app URL.
