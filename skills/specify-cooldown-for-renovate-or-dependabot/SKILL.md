---
name: specify-cooldown-for-renovate-or-dependabot
description: 'Add a one-week cooldown to Dependabot or Renovate dependency update configuration. Use when a repository needs Dependabot `cooldown.default-days: 7`, Renovate `minimumReleaseAge: "7 days"`, or migration from Renovate `stabilityDays` to `minimumReleaseAge`.'
---

# Specify Cooldown For Renovate Or Dependabot

Use this skill when updating an existing repository so dependency update tools wait 7 days before proposing regular version updates.

## 1. Inspect Configuration

Check for Dependabot and Renovate configuration. A repository may have one, both, or neither.

Dependabot:

1. Check for `.github/dependabot.yml`.
2. If it exists, inspect each item in the top-level `updates` list.

Renovate:

1. Check these config locations, in order:
   - `renovate.json`
   - `renovate.jsonc`
   - `renovate.json5`
   - `.github/renovate.json`
   - `.github/renovate.jsonc`
   - `.github/renovate.json5`
   - `.gitlab/renovate.json`
   - `.gitlab/renovate.jsonc`
   - `.gitlab/renovate.json5`
   - `.renovaterc`
   - `.renovaterc.json`
   - `.renovaterc.jsonc`
   - `.renovaterc.json5`
   - `package.json` with a top-level `renovate` object
2. If multiple Renovate configs are present, update only the first effective config unless the user asks to update all of them.

## 2. Update Dependabot

For each Dependabot `updates` entry with a `package-ecosystem`, add this only when that entry does not already have `cooldown`:

```yaml
cooldown:
  default-days: 7
```

Rules:

1. Do not edit an existing `cooldown`, even if it does not use `default-days` or uses a different value.
2. Preserve unrelated keys, comments, ordering, indentation style, and ecosystem-specific settings.
3. Add `cooldown` at the same mapping level as `package-ecosystem`, `directory`, and `schedule`.

Example:

```yaml
- package-ecosystem: github-actions
  directory: /
  schedule:
    interval: weekly
  cooldown:
    default-days: 7
```

## 3. Update Renovate

Add an overall Renovate cooldown by setting:

```json
"minimumReleaseAge": "7 days"
```

Rules:

1. If `minimumReleaseAge` already exists in the effective Renovate config, leave it unchanged.
2. If root-level `stabilityDays` exists and root-level `minimumReleaseAge` does not, replace `stabilityDays` with `minimumReleaseAge: "7 days"`.
3. For `package.json`, apply these rules inside the top-level `renovate` object, not the package manifest root.
4. Preserve the existing file format where practical:
   - JSON files should remain valid JSON.
   - JSONC or JSON5 files may keep comments and trailing commas if they already use them.
   - `.renovaterc` may be JSON or JSONC; inspect before editing.
5. Do not modify `minimumReleaseAge` or `stabilityDays` inside `packageRules` unless the user explicitly asks for package-rule-specific changes.

Examples:

```json
{
  "minimumReleaseAge": "7 days"
}
```

```json
{
  "renovate": {
    "minimumReleaseAge": "7 days"
  }
}
```

## 4. Validate

1. Re-read the changed config files and confirm they remain syntactically valid for their format.
2. Confirm Dependabot entries without an existing `cooldown` now have `cooldown.default-days: 7`.
3. Confirm the effective Renovate config has root-level `minimumReleaseAge: "7 days"` when it did not already define `minimumReleaseAge`.
4. Confirm no unrelated config changes were made.
