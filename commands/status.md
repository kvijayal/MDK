# /status

Shows current MDK project configuration summary.

## Steps

1. Read project config:
   ```bash
   cat .project.json
   ls Services/
   ```

2. Count artifacts:
   ```bash
   find Pages/   -name "*.page"   2>/dev/null | wc -l
   find Actions/ -name "*.action" 2>/dev/null | wc -l
   find Rules/   -name "*.js"     2>/dev/null | wc -l
   grep -c "=" i18n/i18n.properties 2>/dev/null || echo 0
   ```

3. Display:
   - App name + schema version
   - OData service + entity sets
   - Online / offline mode
   - Pages / Actions / Rules / i18n key counts
   - Deployment readiness (.service.metadata present, .build/ exists)
