---
name: pin-github-actions
description: Create or update GitHub Actions workflow files with remote `uses:` entries pinned to full commit SHAs annotated with the resolved release tag. Use whenever creating or updating a workflow file which contains `uses:` entries.
---

# Pin GitHub Actions

## 1. Identify Actions to Pin

1. For the GitHub Actions Workflow file in subject, scan uses of `uses:` entries that reference actions (e.g. `actions/checkout@v6`).
2. For each remote `uses: owner/repo@ref`, determine if it is already pinned to a full commit SHA. If not, it should be pinned.

## 2. Edit the Workflow Files

1. For newly created snippets of workflow files, ensure all `uses:` entries are pinned from the start, with the latest stable release. Use `git ls-remote --tags --sort="v:refname" <REPO_URL> | tail -n 1` to find the latest release tag and corresponding commit SHA.
2. For existing pieces of code, do not update the version unless explicitly instructed. Pin the existing `uses:` entries without changing the version.
3. To pin the action, replace remote refs with the full commit SHA and keep the resolved version as an inline comment (example):

```yaml
uses: actions/checkout@de0fac2e4500dabe0009e67214ff5f5447ce83dd # v6.0.2
```

4. Keep the version comment in the form `# vX.Y.Z` or `# <tag>`, preserve YAML structure and step ordering, and avoid unrelated churn.

## 3. Final Checks

1. Ensure every remote GitHub `uses:` entry points to a 40-character commit SHA.
2. Ensure every pinned remote reference has a matching inline version comment.

## Trigger Examples

1. Create `.github/workflows/ci.yml` for this repo.
2. Update `.github/workflows/release.yml` so actions are up to date.
3. Update all actions used in `.github/workflows/`.
