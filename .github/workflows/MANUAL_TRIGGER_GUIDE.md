# How to Manually Trigger the publish_and_install Workflow

This guide explains how to manually trigger the `publish_and_install.yml` GitHub Actions workflow to test it.

## Prerequisites

- Access to the GitHub repository (https://github.com/oidt/vendure)
- Permissions to trigger workflows (requires write access or higher)

## Steps to Trigger

### Via GitHub Web UI (Recommended)

1. **Navigate to the Actions tab**
   - Go to: https://github.com/oidt/vendure/actions

2. **Select the workflow**
   - In the left sidebar, find and click on **"Publish & Install"**
   - This will show you the workflow runs history

3. **Trigger a new run**
   - Click the **"Run workflow"** dropdown button (top right)
   - Select the branch you want to test (default: `copilot/test-github-action`)
   - Click the **"Run workflow"** button

4. **Monitor the workflow**
   - The workflow will appear in the list with a yellow "in progress" icon
   - Click on the workflow run to see detailed logs
   - The workflow consists of two jobs:
     - `build_and_publish` (runs first, ~5-10 minutes)
     - `test` (runs after, ~10-15 minutes per matrix entry)

### Via GitHub CLI

If you have the GitHub CLI installed:

```bash
# List workflows
gh workflow list

# Run the workflow
gh workflow run "Publish & Install" --ref copilot/test-github-action

# View the workflow runs
gh run list --workflow="Publish & Install"

# Watch a specific run (use the run ID from the list above)
gh run watch <run-id>
```

### Via GitHub API

If you prefer using the API directly:

```bash
# Set your GitHub token
export GITHUB_TOKEN=your_token_here

# Trigger the workflow
curl -X POST \
  -H "Accept: application/vnd.github.v3+json" \
  -H "Authorization: token $GITHUB_TOKEN" \
  https://api.github.com/repos/oidt/vendure/actions/workflows/publish_and_install.yml/dispatches \
  -d '{"ref":"copilot/test-github-action"}'

# List recent workflow runs
curl -H "Accept: application/vnd.github.v3+json" \
  -H "Authorization: token $GITHUB_TOKEN" \
  https://api.github.com/repos/oidt/vendure/actions/workflows/publish_and_install.yml/runs
```

## What to Expect

### For Pull Requests
When triggered from a PR or when a PR is created:
- **1 test job** runs (Ubuntu + Node 22.x)
- Faster feedback (~15-20 minutes total)

### For Push Events
When triggered manually or on push to master/minor/major:
- **9 test jobs** run (all OS and Node combinations)
- Comprehensive testing (~30-45 minutes total)

### Job Details

#### build_and_publish Job
1. Checks out the code
2. Installs Node.js 22.x
3. Sets up Verdaccio local registry
4. Installs dependencies
5. Publishes all packages to Verdaccio
6. Uploads Verdaccio storage as artifact

#### test Job (runs for each matrix entry)
1. Downloads Verdaccio storage from previous job
2. Sets up Node.js (version from matrix)
3. Starts Verdaccio with pre-built packages
4. Creates new Vendure app with `@vendure/create@ci test-app --ci --use-npm --with-storefront`
5. Runs server smoke tests
6. Runs dashboard Playwright tests
7. Uploads screenshots as artifacts

## Viewing Results

### Check Workflow Status
- Green checkmark (✅) = All jobs passed
- Red X (❌) = One or more jobs failed
- Yellow circle (🟡) = In progress
- Gray circle (⚪) = Cancelled

### View Logs
1. Click on a workflow run
2. Click on a job name to see detailed logs
3. Expand sections to see individual step output

### Download Artifacts
1. Scroll to the bottom of the workflow run page
2. Look for "Artifacts" section
3. Download available artifacts:
   - `verdaccio-storage` (packages, 1 day retention)
   - `dashboard-test-screenshots-*` (UI screenshots, 28 days retention)

### Review Screenshots
Screenshots are particularly useful for debugging dashboard tests:
- `dashboard-test-login.png` - After successful login
- `dashboard-test-products.png` - Products page
- `dashboard-test-failure.png` - Captured on test failure

## Automatic Triggers

The workflow also runs automatically on:

### Push Events
When you push to `master`, `minor`, or `major` branches and changes affect:
- `packages/**`
- `package.json`  
- `package-lock.json`

### Pull Request Events
When you create/update a PR to `master`, `minor`, or `major` with changes to the same paths.

## Troubleshooting

### Workflow doesn't appear in the UI
- Ensure the workflow file is in the default branch
- Check that you have the correct repository permissions

### Workflow fails to start
- Verify you selected a valid branch
- Check repository Actions settings (Settings > Actions > General)

### Build fails
- Check the logs for specific error messages
- Common issues:
  - Dependency installation failures
  - Verdaccio connection issues
  - Package build errors

### Test job fails
- Download and review the screenshots
- Check the smoke test logs for API errors
- Verify dashboard test logs for UI issues

## Need Help?

- Check the detailed test report: `.github/workflows/TEST_REPORT.md`
- Review the testing guide: `/tmp/workflow-tests/TESTING_GUIDE.md` (in test environment)
- Check workflow logs for specific error messages

## Example: Successful Run

A successful workflow run will show:

```
✅ build_and_publish
   ├─ Setup Verdaccio
   ├─ Install dependencies
   ├─ Publish to Verdaccio
   └─ Upload artifact

✅ test (ubuntu-latest, 22.x)
   ├─ Setup Verdaccio
   ├─ Install via @vendure/create
   ├─ Server smoke tests (PASSED)
   ├─ Dashboard tests (PASSED)
   └─ Upload screenshots
```

For full matrix runs, you'll see 9 test jobs with different OS/Node combinations.

---

**Ready to test?** Go to https://github.com/oidt/vendure/actions and click "Run workflow"!
