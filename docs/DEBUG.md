# 🛠️ Debugging Halloween Night for Zed

How to develop, test, and troubleshoot the theme locally before publishing.

---

## Quick Debug Loop

```bash
# 1. Edit the theme
vim themes/halloween-night.json

# 2. Install as dev extension (first time only)
#    In Zed: Ctrl+Shift+P → "zed: install dev extension" → select this directory

# 3. Reload (after each edit)
#    In Zed: Ctrl+Shift+P → "zed: reload"

# 4. Preview changes
#    Ctrl+K Ctrl+T → select "Halloween Night"
```

---

## Three Ways to Load the Theme Locally

### A) Dev Extension (best for active development)

```
zed: install dev extension → pick this repo's root dir
```

- Theme is loaded **from disk** — changes take effect after `zed: reload`
- Overrides the published version if installed
- Shows "Overridden by dev extension" in the Extensions page
- To uninstall: `zed: extensions` → find Halloween Night → click Uninstall

### B) Local Themes Directory (simple one-shot)

```bash
mkdir -p ~/.config/zed/themes
cp themes/halloween-night.json ~/.config/zed/themes/halloween-night.json
```

- Restart Zed to pick up
- Must re-copy after every edit
- Good for quick smoke tests

### C) `theme_overrides` in `settings.json` (for tiny tweaks)

Open `~/.config/zed/settings.json` and add:

```json
{
  "theme_overrides": {
    "Halloween Night": {
      "editor.background": "#111111",
      "syntax": {
        "comment": { "font_style": "italic" }
      }
    }
  }
}
```

- No file copies needed
- Overrides are layered *on top* of the theme
- Changes apply instantly

---

## Watching Logs

```bash
# Run Zed in foreground — extension errors print here
zed --foreground

# Or tail the log file
tail -f ~/.local/share/zed/logs/Zed.log
```

Open the log in-editor: `Ctrl+Shift+P` → `zed: open log`

---

## Validating the Theme JSON

```bash
# Syntax check
python3 -m json.tool themes/halloween-night.json > /dev/null && echo "OK" || echo "INVALID"

# Schema check (requires npm)
npx ajv validate -s https://zed.dev/schema/themes/v0.2.0.json -d themes/halloween-night.json
```

---

## Common Issues & Fixes

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Theme doesn't appear in selector | JSON syntax error | Validate JSON; check Zed logs |
| Theme appears but looks wrong / default colors | Schema mismatch (wrong property names) | Compare against [One Dark theme](https://raw.githubusercontent.com/zed-industries/zed/main/assets/themes/one/one.json) |
| Syntax colors don't match VSCode | Tree-sitter captures ≠ TextMate scopes | Zed uses different naming; see [Language Extensions docs](https://zed.dev/docs/extensions/languages#syntax-highlighting) |
| `extension.toml` not found | Wrong directory structure | Extension must be at the root of the dev-extension dir |
| Dev extension install fails | Missing `extension.toml` or bad TOML | Run `cat extension.toml` to verify |
| Published version still loads | Dev extension not overriding | Uninstall published version, re-run `install dev extension` |

---

## Testing Checklist

Before publishing, verify:

- [ ] Theme appears in the **theme selector** (`Ctrl+K Ctrl+T`)
- [ ] **Editor background** is `#202020`, not default
- [ ] **Cursor** is yellow (`#ffcc00`)
- [ ] **Keywords** are red (`#DB6363`)
- [ ] **Strings** are orange (`#FFA977`)
- [ ] **Comments** are purple-gray (`#575599`)
- [ ] **Functions** are teal (`#6ACCB4`)
- [ ] **Types** are green-teal (`#4EC9B0`)
- [ ] **Variables** are lavender (`#c89cfe`)
- [ ] **Sidebar** and **panel** backgrounds are `#1a1a1a`
- [ ] **Tab bar** has distinct active/inactive states
- [ ] **Terminal** ANSI colors match the palette
- [ ] **Git diff** colors (added/modified/deleted) are visible
- [ ] **Search highlights** are visible
- [ ] **No errors** in `zed --foreground` output
- [ ] Theme works in both **editor** and **terminal** panes

---

## Theme Property Reference

### UI Colors (`style.*`)

| Property | Renders where |
|----------|--------------|
| `background` | Main window backdrop |
| `surface.background` | Sidebar, bottom panels |
| `elevated_surface.background` | Dropdowns, popups |
| `editor.background` | Code editor area |
| `editor.gutter.background` | Line number gutter |
| `editor.active_line.background` | Current line highlight |
| `tab_bar.background` | Tab strip |
| `tab.active_background` / `tab.inactive_background` | Individual tabs |
| `title_bar.background` | Top title bar |
| `status_bar.background` | Bottom status bar |
| `panel.background` | Bottom panel (terminal, search, etc.) |
| `scrollbar.thumb.background` | Scrollbar handle |
| `border` / `border.variant` / `border.focused` / `border.selected` | Various borders |

### Syntax Colors (`style.syntax.*`)

Zed uses [tree-sitter](https://tree-sitter.github.io/) captures. Common names:

| Capture | Description |
|---------|-------------|
| `comment` | Code comments |
| `comment.doc` | Doc comments |
| `string` | String literals |
| `string.regex` | Regex strings |
| `string.escape` | Escape sequences |
| `number` | Numeric literals |
| `boolean` | `true` / `false` |
| `constant` | Language constants |
| `keyword` | `if`, `else`, `return`, etc. |
| `function` | Function names |
| `type` | Type names, classes |
| `variable` | Variable identifiers |
| `property` | Object properties |
| `attribute` | HTML/XML attributes |
| `tag` | HTML/XML tags |
| `operator` | `+`, `-`, `=`, etc. |
| `punctuation` | `,`, `;`, `.`, etc. |
| `punctuation.bracket` | `()`, `{}`, `[]` |

### Status Colors (`style.*`)

| Property | Where used |
|----------|-----------|
| `error` / `error.background` / `error.border` | Diagnostics, git deleted |
| `warning` / `warning.background` / `warning.border` | Warnings |
| `info` / `info.background` / `info.border` | Info messages |
| `success` / `success.background` / `success.border` | Success messages |
| `hint` / `hint.background` / `hint.border` | Inlay hints |
| `created` / `modified` / `deleted` | Git status in file tree |
| `conflict` | Merge conflicts |
| `predictive` | AI-generated code |
