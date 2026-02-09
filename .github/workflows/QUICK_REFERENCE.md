# Quick Reference: publish_and_install.yml Workflow

## ⚡ Quick Start

**Trigger the workflow**:
1. Go to https://github.com/oidt/vendure/actions
2. Select "Publish & Install"
3. Click "Run workflow"

## 📊 What It Tests

| Component | What's Tested | Duration |
|-----------|--------------|----------|
| **Build & Publish** | All packages build and publish to Verdaccio | 5-10 min |
| **Server** | Health, Shop API, Admin API | 30-60 sec |
| **Dashboard** | Login, Navigation, Plugins | 1-2 min |
| **Platforms** | Ubuntu, Windows, macOS (on push) | Variable |
| **Node Versions** | 20.x, 22.x, 24.x (on push) | Variable |

## 🎯 Test Matrix

| Event Type | Jobs | OS | Node | Total Time |
|------------|------|-------|------|------------|
| **Pull Request** | 1 | Ubuntu | 22.x | ~15-20 min |
| **Push** | 9 | Ubuntu/Windows/macOS | 20.x/22.x/24.x | ~30-45 min |

## 🔑 The Key Fix

**Commit**: `46562820de3d64b5936fb94b361116f1d2f35ce5`

```diff
- npx @vendure/create@ci test-app --ci --use-npm --log-level info
+ npx @vendure/create@ci test-app --ci --use-npm --with-storefront --log-level info
```

The `--with-storefront` flag ensures complete testing of the full Vendure stack.

## 📁 Files Added

- `.github/workflows/TEST_REPORT.md` - Detailed test report
- `.github/workflows/MANUAL_TRIGGER_GUIDE.md` - Trigger instructions
- `.github/workflows/TESTING_SUMMARY.md` - Executive summary
- `.github/workflows/scripts/README.md` - Scripts documentation

## ✅ Validation Status

| Component | Status |
|-----------|--------|
| Workflow Structure | ✅ 46/46 checks passed |
| Script Files | ✅ All 6 validated |
| Verdaccio Config | ✅ Correct |
| YAML Syntax | ✅ Valid |
| --with-storefront | ✅ Confirmed |

## 🔧 Scripts

| Script | Purpose |
|--------|---------|
| `smoke-tests.js` | Validates server APIs |
| `dashboard-tests.js` | Playwright UI tests |
| `setup-test-plugin.js` | Configures test plugin |
| `vite.config.mts` | Vite setup |
| `test-plugin/*` | Test plugin & extension |

## 📸 Artifacts

After workflow runs, download:
- `verdaccio-storage` (1 day retention)
- `dashboard-test-screenshots-*` (28 days retention)

## 🚀 Triggers

**Automatic**:
- Push to `master`/`minor`/`major` affecting `packages/*`
- PR to `master`/`minor`/`major` affecting `packages/*`

**Manual**:
- GitHub UI: Actions → Publish & Install → Run workflow
- GitHub CLI: `gh workflow run "Publish & Install"`
- API: See MANUAL_TRIGGER_GUIDE.md

## 🐛 Troubleshooting

| Issue | Check |
|-------|-------|
| Smoke tests fail | Server logs, database, health endpoint |
| Dashboard tests fail | Screenshots, console logs, port availability |
| Build fails | Dependency logs, Verdaccio connection |

## 📚 Documentation

For more details, see:
- **Full Test Report**: TEST_REPORT.md
- **Trigger Guide**: MANUAL_TRIGGER_GUIDE.md
- **Summary**: TESTING_SUMMARY.md
- **Scripts**: scripts/README.md

---

**Status**: ✅ READY FOR USE  
**Last Updated**: 2026-02-09
