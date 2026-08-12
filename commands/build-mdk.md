# /build-mdk

Builds the MDK project bundle for deployment.

## Steps

1. Validate first:
   ```bash
   npx @sap/mdk-tools validate --project .
   ```
   Stop if there are errors.

2. Build:
   ```bash
   npx @sap/mdk-tools build --target zip --project .
   ```

3. Confirm bundle at `.build/` and report size.
4. Suggest `/deploy-mdk` as next step.
