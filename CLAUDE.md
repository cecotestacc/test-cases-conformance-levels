# Test Cases — Conformance Levels

This file is read automatically by Claude Code when working in this folder.
Follow every rule here when creating or editing test cases.

---

## Purpose

One HTML file per supported axe-core rule, named after the rule ID. Each file contains exactly **one** accessibility issue — the intended violation or needs-review trigger for that rule. Everything else on the page must be fully accessible (no secondary violations).

The authoritative rule list is at:
```
C:\Users\TsvetomirTsanov\Desktop\QB-ScheduledScanning\axe-and-behaviour-rules-to-sc-and-levels.md
```

Rules marked `[not supported]` do **not** get a file here. Rules marked `[behaviour]` **do** get a file here.

---

## Repo layout

```
test-cases-conformance-levels/
  {rule-id}.html          ← one file per axe-core rule
  base-accessible.html    ← fully accessible reference page (0 violations)
```

Flat structure — no subfolders.

---

## Filename rules

- **Filename = axe-core rule ID exactly.** e.g. `image-alt.html`, `color-contrast.html`.
- One file per rule. Do not create multiple files for the same rule.
- Do not create files for `[not supported]` rules. `[behaviour]` rules do get a file.

---

## Test case requirements

- **Exactly 1 issue per file.** The axe-core scan must report either 1 violation or 1 incomplete (needs-review) result — no more, no less.
- **Clean baseline.** Start from `base-accessible.html`. Add only the minimal markup needed to trigger the target rule.
- **No secondary violations.** If a pattern causes cascading issues, adjust the markup to suppress them (e.g. add `aria-label` to suppress a name violation when testing a different rule).
- **[needs review] rules.** Ten rules are marked `[needs review]` in the mapping file — they always return "incomplete" rather than "violation" in axe-core by design. Test cases for these rules are still required; they must trigger the incomplete state.
- **AAA and WCAG 2.2 rules.** `color-contrast-enhanced`, `meta-refresh-no-exceptions`, and `target-size` only fire when invoked with `--rules <rule-id>`. This is expected.

---

## Scanning

Use a local HTTP server and axe-core CLI to verify each file:

```bash
# Start server (from repo root)
npx http-server test-cases-conformance-levels -p 7788 --silent &

# Scan a single file
./node_modules/.bin/axe http://localhost:7788/{rule-id}.html --chrome-options="--no-sandbox,--headless"

# Save full results (including incomplete) to JSON
./node_modules/.bin/axe http://localhost:7788/{rule-id}.html --chrome-options="--no-sandbox,--headless" --save result.json
node -e "const d=require('./result.json')[0]; console.log('violations:',d.violations.length,'incomplete:',d.incomplete.length)"
```

Chrome path: `C:/Program Files/Google/Chrome/Application/chrome.exe`
ChromeDriver: `node_modules/chromedriver/lib/chromedriver/chromedriver.exe` (must match Chrome version — run `node node_modules/chromedriver/install.js` if version mismatch)

---

## QualiBooth scan config sync

Every HTML test file has a live GitHub Pages URL that must be kept in sync with the QualiBooth scan config. Do this in the **same operation** as the file change, before committing.

### Reference IDs

| Key | Value |
|---|---|
| Environment | QualiBooth **dev** (`mcp__claude_ai_QualiBooth__*`) |
| Org | `ceco` |
| Org UUID | `a0f273d0-9765-403e-98dc-7bbb59c70c60` |
| Project | `cecotestacc.github.io` |
| Project UUID | `7bd2f8f7-66da-4b74-9f4d-bf15eb2b3050` |
| Scan config name | `ConformanceLevels-tests` |
| Scan config UUID | `TBD — update after first config creation` |
| Base URL | `https://cecotestacc.github.io/test-cases-conformance-levels/` |

### URL construction

```
{rule-id}.html
→ https://cecotestacc.github.io/test-cases-conformance-levels/{rule-id}.html
```

### Actions

**File added** — call `mcp__claude_ai_QualiBooth__add_scan_config_urls`:
```
scanConfigUuid: <uuid from table above>
urls: ["https://cecotestacc.github.io/test-cases-conformance-levels/{rule-id}.html"]
confirmed: true
```

**File renamed** — three steps in order:
1. `list_scan_config_urls` — find old URL UUID
2. `delete_scan_config_urls` — remove old URL
3. `add_scan_config_urls` — add new URL

**File deleted** — `list_scan_config_urls` to find UUID, then `delete_scan_config_urls`.

### Rules
- Always use **dev** MCP (`mcp__claude_ai_QualiBooth__*`), never prod.
- The scan config is `ON_DEMAND` — `confirmed: true` is always safe.
- Do not add non-HTML files (`base-accessible.html`, `test-patterns.html`, `.md`, `.json`) to the scan config.

---

## Git commit

Stage only HTML files in this folder. Commit message format:

```
add conformance-level test cases for axe-core rules
```

or for individual additions:

```
add test case: {rule-id}
```

Co-Authored-By line is required:
```
Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
```
