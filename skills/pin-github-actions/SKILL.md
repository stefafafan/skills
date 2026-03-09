---
name: pin-github-actions
description: Create or update GitHub Actions workflow files with remote `uses:` entries pinned to full commit SHAs annotated with the resolved release tag. Use when authoring or editing `.github/workflows/*.yml` or `.yaml`, replacing floating refs such as `@v4`, `@main`, or `@master`, or refreshing workflows so every remote action is pinned to the latest appropriate stable version.
---

# Pin GitHub Actions

## Overview

Create or update workflow YAML with exact pins for every remote GitHub Action. Resolve the correct release or tag first, then replace the floating ref with the full 40-character commit SHA and keep the human-readable version as an inline comment.

## Classify `uses:` Values

1. Inspect every `uses:` entry before editing.
2. Treat `owner/repo@ref` and `owner/repo/path@ref` as remote GitHub references that should usually be pinned.
3. Leave local references such as `./.github/actions/foo` unchanged.

## Resolve The Correct Version

1. If the user gives an exact version, tag, or commit, honor that instead of choosing a different version.
2. If the user asks to pin the existing version line, resolve the newest stable patch release within that line. Example: convert `@v4` to the latest stable `v4.x.y`.
3. If the user asks for the latest version available, resolve the latest stable release overall, even if that changes majors.
4. Do not guess from memory when determining the latest version. Verify against the upstream repository's Releases or Tags.
5. Prefer the latest stable GitHub Release. If the repository does not publish Releases, use the latest stable semver tag.
6. Ignore prereleases unless the user explicitly asks for them or no stable release exists.
7. Resolve the commit SHA behind the chosen tag and use the full 40-character SHA, not an abbreviated hash.

## Write The Pin

Use this format for remote GitHub references:

```yaml
uses: actions/checkout@de0fac2e4500dabe0009e67214ff5f5447ce83dd # v6.0.2
```

1. Keep the version comment in the form `# vX.Y.Z` or `# <tag>` when the upstream tag is not semver.
2. Preserve YAML indentation and surrounding structure.
3. Keep `with:`, `env:`, `permissions:`, `if:`, `name:`, and step ordering intact unless the user asked for broader workflow changes.
4. If an existing inline comment contains other context, move that context to a separate comment line if needed so the pin comment stays unambiguous.
5. Do not leave floating remote refs such as `@v4`, `@v6`, `@main`, or `@master` in the final workflow unless the user explicitly asks for an unpinned ref.

## Create Or Update Workflows

1. When creating a new workflow, draft the workflow normally, then pin every remote `uses:` entry before finishing.
2. When updating an existing workflow, minimize churn outside the requested changes and the necessary pin updates.
3. Preserve local composite actions referenced via `./` unless the request says otherwise.
4. If an action is archived, deprecated, or lacks a trustworthy stable release, surface that clearly before swapping to a different action.

## Final Checks

1. Ensure every remote GitHub `uses:` entry points to a 40-character commit SHA.
2. Ensure every pinned remote reference has a matching inline version comment.
3. Ensure local `./` references remain untouched unless explicitly requested.
4. Ensure the workflow has no unrelated YAML churn.

## Trigger Examples

1. Create `.github/workflows/ci.yml` for this repo.
2. Update `.github/workflows/release.yml` so `actions/checkout` and `actions/setup-node` use the latest versions.
3. Update all actions used in `.github/workflows/`.
