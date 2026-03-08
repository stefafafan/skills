---
name: go-runtime-updater
description: Pick the correct Go version for `go` and `toolchain` directives. Use when an agent needs to bump the Go version, update a go or toolchain directive, or align Go versions in go.mod, go.work, CI, containers, version manager files, or docs.
---

# Go Runtime Updater

## 1. Determine The Target Version

If the user did not give an explicit target, determine the latest exact stable Go version first.

1. Check [pkg.go.dev/std](https://pkg.go.dev/std) and read the `Version: goX.Y.Z` line. Treat that as the latest exact stable release.
2. Do not infer the latest Go version from arbitrary `pkg.go.dev` search results or a random package page. Use `pkg.go.dev/std`.
3. Derive the latest release line from that exact version by replacing the patch with `.0`. Example: `go1.26.1` maps to release line `1.26.0`.
4. Derive the previous major release line by subtracting `1` from the minor version and using patch `.0`. Example: if the latest release line is `1.26.0`, the previous major release line is `1.25.0`.
5. When updating a `go` directive, use the relevant release line with patch `.0`, not the latest patch release.

## 2. Update `go.mod` And `go.work`

1. If a `toolchain go...` directive exists and the user wants the latest version, update only `toolchain` to the latest exact release. Do not change `go`.
   Use `go mod edit -toolchain=goX.Y.Z` or `go work edit -toolchain=goX.Y.Z`.
2. If a `toolchain go...` directive exists and the user wants a specific version, update only `toolchain` to that version. Do not change `go`.
   Use `go mod edit -toolchain=goX.Y.Z` or `go work edit -toolchain=goX.Y.Z`.
3. If there is no `toolchain` directive and the user wants a specific version that is not the latest release line, update `go` to that release line with patch `.0`.
   Use `go mod edit -go=X.Y.0` or `go work edit -go=X.Y.0`.
4. If there is no `toolchain` directive and the user wants the latest version or latest major version, ask whether they want to raise the minimum supported Go version or keep compatibility by adding a `toolchain` directive instead.
5. Do not add a new `toolchain` directive unless the user asks for it or confirms that they want the latest toolchain without raising the minimum supported Go version.
6. Preserve formatting and unrelated directives.

## 3. Best Practice

1. `go` is the minimum required Go version.
2. `toolchain` is a suggested toolchain for working in the module or workspace.
3. Do not add `toolchain` by default; use it when the project wants a newer preferred toolchain without changing the minimum supported Go version.

## 4. Examples

1. Latest exact release is `go1.26.1`, file has `go 1.25.0` and `toolchain go1.25.7`: update only to `toolchain go1.26.1`.
2. User requests `1.24.6`, file has only `go 1.23.0`: update to `go 1.24.0`.
3. User requests "latest", file has only `go 1.25.0`: ask whether to change the minimum supported version to `go 1.26.0` or add `toolchain go1.26.1`.
4. Latest exact release is `go1.26.1`: the previous major release line is `1.25.0`.
