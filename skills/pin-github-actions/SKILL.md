---
name: pin-github-actions
description: Create or update GitHub Actions workflow files with remote `uses:` entries pinned to full commit SHAs annotated with the resolved release tag. Use whenever creating or updating a workflow file which contains `uses:` entries.
---

# Pin GitHub Actions

## 1. Check whether to use `pinact` and install if needed
1. Search the repository for `.pinact.yaml` and `.pinact.yml`.
2. Only if a config file exists anywhere in the repo, install [pinact](https://github.com/suzuki-shunsuke/pinact).

## 2-a. If `pinact` should be used, run it with the config file
1. Run `pinact run --config <path to config file>` from the repository root

## 2-b. If `pinact` should not be used, edit the workflow file directly

1. Pin `uses:` entries such as `owner/repo@ref`
2. Resolve the latest appropriate stable release or stable patch line from the upstream Releases or Tags; do not guess from memory.
3. Replace remote refs with the full 40-character commit SHA and keep the resolved version as an inline comment (example):

```yaml
uses: actions/checkout@de0fac2e4500dabe0009e67214ff5f5447ce83dd # v6.0.2
```

4. Keep the version comment in the form `# vX.Y.Z` or `# <tag>`, preserve YAML structure and step ordering, and avoid unrelated churn.
5. If an action is archived, deprecated, or lacks a trustworthy stable release, surface that clearly to the user and ask how to proceed.

## 3. Final Checks

1. If `pinact` was used, check if the command succeeded.
2. Otherwise ensure every remote GitHub `uses:` entry points to a 40-character commit SHA.
3. Ensure every pinned remote reference has a matching inline version comment.

## Trigger Examples

1. Create `.github/workflows/ci.yml` for this repo.
2. Update `.github/workflows/release.yml` so actions are up to date.
3. Update all actions used in `.github/workflows/`.
