# 📦 Publishing Halloween Night to the Zed Extensions Registry

Step-by-step guide to publish this theme so anyone can install it from Zed's built-in extensions browser.

---

## Pre-flight Checks

Before you start, make sure:

- [ ] This repo is **public** on GitHub
- [ ] `LICENSE.md` is present and is one of the [accepted licenses](https://zed.dev/docs/extensions/developing-extensions#extension-license-requirements) (MIT ✅)
- [ ] `extension.toml` has a **unique `id`** that doesn't collide with any [existing extension](https://github.com/zed-industries/extensions/tree/main/extensions)
- [ ] The theme works as a **dev extension** locally (see [DEBUG.md](./DEBUG.md))
- [ ] Version in `extension.toml` is bumped appropriately
- [ ] All changes are **committed and pushed** to GitHub

---

## 1. Fork the Extensions Repository

Go to [zed-industries/extensions](https://github.com/zed-industries/extensions) and click **Fork**.

> ⚠️ **Fork to your personal account**, not an organization. This lets Zed maintainers push directly to your PR branch if needed.

---

## 2. Clone Your Fork

```bash
git clone https://github.com/YOUR_GITHUB_USERNAME/extensions.git
cd extensions
git submodule init
git submodule update
```

---

## 3. Add Your Extension as a Submodule

```bash
# From inside the extensions/ directory
git submodule add https://github.com/BlueRexPY/HalloweenNightZed.git extensions/halloween-night-theme
```

The submodule path under `extensions/` **must match** the `id` in your `extension.toml`:

```toml
# extension.toml (in your theme repo)
id = "halloween-night-theme"     # ← this

# Submodule path in extensions repo
extensions/halloween-night-theme  # ← must match exactly
```

> ⚠️ Use **HTTPS** URLs for submodules (`https://github.com/...`), **not** SSH (`git@github.com:...`).

> ⚠️ The checked-out commit must be on a **branch**, not a detached HEAD. Verify with:
> ```bash
> cd extensions/halloween-night-theme
> git branch  # should show `* main` or similar
> ```

---

## 4. Register the Extension in `extensions.toml`

Edit `extensions.toml` at the root of the `extensions` repo:

```toml
[halloween-night-theme]
submodule = "extensions/halloween-night-theme"
version = "0.3.3"
```

If your `extension.toml` is in a subdirectory of your repo, add a `path`:

```toml
[halloween-night-theme]
submodule = "extensions/halloween-night-theme"
path = "packages/zed"           # optional: only if extension.toml isn't at repo root
version = "0.3.3"
```

---

## 5. Sort the Manifest

```bash
# From the root of the extensions repo
pnpm sort-extensions
```

This alphabetically sorts both `extensions.toml` and `.gitmodules`.

If you don't have `pnpm`:

```bash
npm install -g pnpm
```

---

## 6. Commit and Push

```bash
git add extensions/halloween-night-theme
git add extensions.toml
git add .gitmodules
git commit -m "Add Halloween Night theme"
git push origin main
```

---

## 7. Open a Pull Request

Go to your fork on GitHub and open a PR against `zed-industries/extensions:main`.

PR title example:
```
Add Halloween Night theme
```

PR description template:
```markdown
## Description
Ported Halloween Night theme from VS Code to Zed.

## Checklist
- [x] Tested locally as a dev extension
- [x] License is MIT (accepted)
- [x] Extension ID is unique: `halloween-night-theme`
- [x] Extension ID is suffixed with `-theme`
- [x] Only includes theme-related resources
- [x] No custom Rust/WASM code (theme-only extension)

## Links
- [Source repository](https://github.com/BlueRexPY/HalloweenNightZed)
- [Original VS Code theme](https://github.com/BlueRexPY/HalloweenNight)
```

---

## 8. After Merge

- Your extension is automatically **packaged and published** to the Zed registry
- It appears in `zed: extensions` within minutes
- Users can install it with one click

---

## Updating an Existing Extension

When you make changes to your theme and want to release a new version:

### In your theme repo

```bash
cd HalloweenNightZed
# Make your changes...
# Bump version in extension.toml
git add -A
git commit -m "v0.4.0: adjust comment colors"
git push origin main
```

### In the extensions repo

```bash
cd extensions

# Update the submodule to the latest commit
git submodule update --remote extensions/halloween-night-theme

# Update version in extensions.toml
# Change: version = "0.3.3" → version = "0.4.0"

# Sort
pnpm sort-extensions

# Commit and push
git add extensions/halloween-night-theme extensions.toml
git commit -m "Update Halloween Night to v0.4.0"
git push origin main
```

### Automate with GitHub Actions

Add `.github/workflows/update-extension.yml` to your theme repo:

```yaml
name: Update Extension in Registry

on:
  push:
    branches: [main]
    paths:
      - 'extension.toml'

jobs:
  update:
    runs-on: ubuntu-latest
    steps:
      - name: Trigger extensions repo update
        uses: peter-evans/repository-dispatch@v3
        with:
          token: ${{ secrets.EXTENSIONS_REPO_PAT }}
          repository: YOUR_USERNAME/extensions
          event-type: update-halloween-night
```

> This requires a [Personal Access Token](https://github.com/settings/tokens) with `repo` scope stored as a secret in your theme repo.

---

## Publishing Checklist (Quick Reference)

```
☐ extension.toml: id is unique and suffixed with -theme
☐ extension.toml: version is correct
☐ extension.toml: repository URL points to the correct GitHub repo
☐ LICENSE.md is present and accepted (MIT, Apache-2.0, etc.)
☐ Theme tested locally as dev extension
☐ All changes pushed to GitHub
☐ Forked zed-industries/extensions (personal account, not org)
☐ Submodule added with HTTPS URL
☐ Submodule commit is on a branch (not detached)
☐ extensions.toml entry added with matching submodule path and version
☐ Ran pnpm sort-extensions
☐ PR opened against zed-industries/extensions:main
```

---

## Links

- [Zed Extension Development Docs](https://zed.dev/docs/extensions/developing-extensions)
- [Zed Extensions Repo](https://github.com/zed-industries/extensions)
- [Extension Publishing Prerequisites](https://zed.dev/docs/extensions/developing-extensions#extension-publishing-prerequisites)
- [Accepted Extension Licenses](https://zed.dev/docs/extensions/developing-extensions#extension-license-requirements)
