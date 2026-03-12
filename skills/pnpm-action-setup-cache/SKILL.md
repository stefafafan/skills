---
name: pnpm-action-setup-cache
description: "Update GitHub Actions workflows to use pnpm/action-setup cache support introduced in v4.3.0. Use when creating or editing workflow YAML files that install pnpm, especially when they use actions/setup-node cache: pnpm or actions/cache entries for pnpm store paths."
---

# pnpm/action-setup Cache Migration

## 1. Inspect Workflow Files

1. Open the target `.github/workflows/*.yml` or `.yaml` files.
2. Locate steps using `pnpm/action-setup`.
3. Record the current `uses: pnpm/action-setup@...` version.
4. Check whether caching is configured with either:
   - `actions/setup-node` using `cache: pnpm`, or
   - `actions/cache` targeting pnpm store directories.

## 2. Upgrade pnpm/action-setup and Enable Built-in Cache

1. If `pnpm/action-setup` is older than `v4.3.0`, update it to a modern tag (`v4.3.0` or newer).
2. Ensure the action input enables built-in caching:

```yaml
- uses: pnpm/action-setup@v4.3.0
  with:
    cache: true
```

3. Keep existing `version` (pnpm CLI version) or other unrelated inputs unless the user explicitly asks to change them.

## 3. Remove Legacy pnpm Cache Configuration

1. Remove pnpm cache wiring from `actions/setup-node` steps:
   - delete `cache: pnpm`
   - delete `cache-dependency-path` values that were only used for pnpm caching
2. Remove dedicated `actions/cache` steps that cache pnpm store directories (for example paths from `pnpm store path` or `~/.pnpm-store`).
3. Preserve non-pnpm caches and unrelated setup-node behavior (Node version selection, registry config, auth).

## 4. Validate Final Workflow Shape

1. Confirm each pnpm workflow now has exactly one cache strategy for pnpm: `pnpm/action-setup` with `cache: true`.
2. Confirm no leftover pnpm-specific cache keys or paths remain in `actions/setup-node` or `actions/cache` steps.
3. Keep edits minimal and avoid unrelated workflow churn.

## Example Migration

Before:

```yaml
- uses: pnpm/action-setup@v4.2.0
- uses: actions/setup-node@v4
  with:
    node-version-file: .nvmrc
    cache: pnpm
```

After:

```yaml
- uses: pnpm/action-setup@v4.3.0
  with:
    cache: true
- uses: actions/setup-node@v4
  with:
    node-version-file: .nvmrc
```
