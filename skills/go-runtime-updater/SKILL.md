---
name: go-runtime-updater
description: Pick the correct Go version for `go` and `toolchain` directives. Use when an agent needs to bump the Go version, update a go or toolchain directive, or align Go versions in go.mod, go.work, CI, containers, version manager files, or docs.
---

# Go Runtime Updater

## Pick And Apply The Version

If the user did not give an explicit target, determine the latest exact stable Go version first.

- Check [pkg.go.dev/std](https://pkg.go.dev/std) and read the `Version: goX.Y.Z` line. Treat that as the latest exact stable release.
- Do not infer the latest Go version from arbitrary `pkg.go.dev` search results or a random package page. Use `pkg.go.dev/std`.
- Then check [Go release history](https://go.dev/doc/devel/release) to map that exact version to its release line.
- On the release history page, the first `## goX.Y.0` heading is the latest release line, not necessarily the latest exact version.
- If that section has `### Minor revisions`, the newest `goX.Y.Z` under it is the latest exact release for that line. This should match `pkg.go.dev/std`.
- If `pkg.go.dev/std` and the release history page appear to disagree, prefer `pkg.go.dev/std` for the exact latest stable version and use the release history page only to confirm the matching `goX.Y.0` line.
- When updating a `go` directive, use the relevant release line with patch `.0`, not the latest patch release.

In `go.mod` and `go.work`:

- If a `toolchain go...` directive exists and the user wants the latest version, update only `toolchain` to the latest exact release. Do not change `go`.
  Use `go mod edit -toolchain=goX.Y.Z` or `go work edit -toolchain=goX.Y.Z`.
- If a `toolchain go...` directive exists and the user wants a specific version, update only `toolchain` to that version. Do not change `go`.
  Use `go mod edit -toolchain=goX.Y.Z` or `go work edit -toolchain=goX.Y.Z`.
- If there is no `toolchain` directive and the user wants a specific version that is not the latest major version, update `go` to that release line with patch `.0`.
  Use `go mod edit -go=X.Y.0` or `go work edit -go=X.Y.0`.
- If there is no `toolchain` directive and the user wants the latest version or latest major version, ask whether they want to raise the minimum supported Go version or keep compatibility by adding a `toolchain` directive instead.
- Do not add a new `toolchain` directive unless the user asks for it or confirms that they want the latest toolchain without raising the minimum supported Go version.
- Preserve formatting and unrelated directives.

Best practice: `go` is the minimum required Go version. `toolchain` is a suggested toolchain for working in the module or workspace. Do not add `toolchain` by default; use it when the project wants a newer preferred toolchain without changing the minimum supported Go version.

Examples:

- Latest exact release is `go1.26.1`, file has `go 1.25.0` and `toolchain go1.25.7`: update only to `toolchain go1.26.1`.
- User requests `1.24.6`, file has only `go 1.23.0`: update to `go 1.24.0`.
- User requests "latest", file has only `go 1.25.0`: ask whether to change the minimum supported version or add `toolchain go1.26.1`.
