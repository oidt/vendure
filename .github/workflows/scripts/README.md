# GitHub Actions Workflow Scripts

This directory contains scripts used by the `publish_and_install.yml` GitHub Actions workflow.

## Overview

These scripts support comprehensive end-to-end testing of Vendure package publication and installation across multiple platforms.

## Scripts

### smoke-tests.js

**Purpose**: Validates basic server functionality after installation

**What it tests**:
- Health endpoint (`/health`)
- Shop API GraphQL endpoint (products query)
- Admin API GraphQL endpoint (login mutation)

**Usage in workflow**:
```bash
npm run dev &
node $GITHUB_WORKSPACE/.github/workflows/scripts/smoke-tests
```

**Exit codes**:
- `0`: All tests passed
- `1`: One or more tests failed

**Expected duration**: 30-60 seconds

---

### dashboard-tests.js

**Purpose**: Browser-based UI testing using Playwright

**What it tests**:
- Dashboard loads correctly (http://localhost:5173)
- Login functionality works
- Navigation to Products page
- Test plugin component renders
- UI responsiveness

**Usage in workflow**:
```bash
npm run dev &
npx vite --port 5173 &
node $GITHUB_WORKSPACE/.github/workflows/scripts/dashboard-tests.js
```

**Screenshots captured**:
- `dashboard-test-login.png` - After successful login
- `dashboard-test-products.png` - Products page
- `dashboard-test-failure.png` - On test failure

**Exit codes**:
- `0`: All tests passed
- `1`: One or more tests failed

**Expected duration**: 1-2 minutes

---

### setup-test-plugin.js

**Purpose**: Modifies vendure-config.ts to include the test plugin

**What it does**:
1. Reads `src/vendure-config.ts`
2. Adds import for TestPlugin
3. Adds TestPlugin to plugins array

**Usage in workflow**:
```bash
cp test-plugin files to src/plugins/test-plugin
node setup-test-plugin.js
```

**Note**: This script modifies the config file in place. It's safe because it runs on a fresh installation.

---

### vite.config.mts

**Purpose**: Vite configuration for dashboard development and testing

**Key settings**:
- Output directory: `dist/dashboard`
- Dashboard plugin configured
- GraphQL code generation setup
- Path aliases for `@/gql`

**Usage in workflow**:
```bash
cp vite.config.mts to test-app directory
npx vite --port 5173
```

---

### test-plugin/

**Purpose**: Minimal plugin for testing dashboard extension capabilities

#### test-plugin/test.plugin.ts

Vendure plugin that:
- Imports PluginCommonModule
- References dashboard extension
- Compatible with Vendure 3.0.0+

```typescript
@VendurePlugin({
    imports: [PluginCommonModule],
    dashboard: './dashboard/index.tsx',
    compatibility: '>=3.0.0',
})
export class TestPlugin {}
```

#### test-plugin/dashboard/index.tsx

Dashboard extension that:
- Adds a test component to the product-list page
- Component has `data-testid="test-component"` for testing
- Used by dashboard-tests.js to verify plugin compilation

```tsx
defineDashboardExtension({
    actionBarItems: [{
        pageId: 'product-list',
        component: () => <div data-testid="test-component">test component</div>
    }]
});
```

## Workflow Integration

These scripts are used in the `publish_and_install.yml` workflow:

1. **Build & Publish Job**:
   - No scripts used (pure build and publish)

2. **Test Job**:
   ```yaml
   # Copy scripts
   - Copy vite.config.mts
   - Copy test-plugin files
   - Copy setup-test-plugin.js
   
   # Run setup
   - node setup-test-plugin.js
   
   # Run tests
   - node smoke-tests.js
   - node dashboard-tests.js
   ```

## Development

### Adding New Tests

To add new smoke tests:
1. Edit `smoke-tests.js`
2. Add new test functions following existing patterns
3. Call from `runTests()` function

To add new dashboard tests:
1. Edit `dashboard-tests.js`
2. Add new Playwright test steps
3. Add appropriate assertions

### Testing Locally

You can test these scripts locally:

```bash
# Start a dev server
cd packages/dev-server
npm run dev

# In another terminal, run smoke tests
node .github/workflows/scripts/smoke-tests.js

# For dashboard tests (requires Playwright)
npm install playwright
npx playwright install chromium
cd packages/dev-server
npx vite &
node ../../.github/workflows/scripts/dashboard-tests.js
```

### Dependencies

**smoke-tests.js**:
- Node.js built-in `http` module
- Running Vendure server on port 3000

**dashboard-tests.js**:
- `playwright` package
- Chromium browser
- Running dashboard on port 5173
- Running Vendure server on port 3000

**setup-test-plugin.js**:
- Node.js built-in `fs` and `path` modules

## Troubleshooting

### Smoke tests fail

**Symptom**: Tests timeout or return unexpected data

**Solutions**:
- Ensure server is running on port 3000
- Check server logs for errors
- Verify database is populated
- Check health endpoint manually: `curl http://localhost:3000/health`

### Dashboard tests fail

**Symptom**: Playwright can't find elements

**Solutions**:
- Ensure dashboard is running on port 5173
- Check browser console in screenshots
- Verify test plugin compiled correctly
- Check that ports aren't blocked

**Common issues**:
- Port 3000 or 5173 already in use
- Playwright browser not installed
- Dashboard build errors

### Setup script fails

**Symptom**: Cannot modify vendure-config.ts

**Solutions**:
- Verify the file exists at `src/vendure-config.ts`
- Check file permissions
- Ensure config has a `plugins:` array

## Maintenance

### When to Update

Update these scripts when:
- API endpoints change
- Dashboard UI structure changes
- New testing scenarios are needed
- Plugin API changes

### Best Practices

1. **Keep tests minimal**: Only test what's necessary
2. **Use data-testid**: Makes tests more resilient
3. **Capture screenshots**: Helps debugging in CI
4. **Add timeouts**: Prevent hanging in CI
5. **Test locally**: Before committing changes

## Related Documentation

- Workflow test report: `TEST_REPORT.md`
- Manual trigger guide: `MANUAL_TRIGGER_GUIDE.md`
- Testing summary: `TESTING_SUMMARY.md`
- Main workflow: `publish_and_install.yml`

---

**Last Updated**: 2026-02-09  
**Maintainer**: Vendure Core Team
