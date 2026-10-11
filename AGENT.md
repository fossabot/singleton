# Repository agent instructions

## Architecture

- Use ports and adapters architecture, organized by feature. Keep each feature's domain rules, application use cases, ports, and adapters together under `internal/`; reuse the existing layers when extending the singleton feature.
- Keep domain logic independent of adapters and third-party libraries. Application code depends on domain contracts and ports; adapters implement those ports. Wire concrete adapters at the composition boundary.
- Keep the root `singleton` package as the public facade. Put implementation details in `internal/` and expose only the contracts consumers need.

## Go package files

Every Go package may contain only the following Go filenames. Replace `PACKAGENAME` with the actual package name; create only files that have content.

| Filename | Contents |
| --- | --- |
| `funcs.go` | Functions and methods |
| `vars.go` | Package variables, including sentinel errors |
| `consts.go` | Constants |
| `types.go` | Type declarations and package documentation |
| `PACKAGENAME_test.go` | Tests of unexported implementation, using `package PACKAGENAME` |
| `PACKAGENAME_integration_test.go` | Tests of exported behavior, using `package PACKAGENAME_test` and only exported APIs |
| `PACKAGENAME_bench_test.go` | Benchmarks |

Place package documentation on the package declaration in `types.go`. Consolidate Go code into the allowed filenames when changing a package; names such as `doc.go`, `errors.go`, and additional test filenames are outside this convention. Non-Go repository files are unaffected.

## Public API boundaries

- Keep implementation types, fields, helpers, and state unexported wherever possible. Export symbols inside `internal/` only when another internal package needs them.
- Public signatures, exported fields, type aliases, and interface methods must use public facade types or standard-library types. Keep internal types and third-party adapter types out of the public API.
- Translate implementation errors, events, and options at the facade boundary. Expose public error contracts without leaking concrete internal or third-party errors through error chains.
- Use internal contracts only in private implementation code. Keep consumers independent of internal packages and concrete adapters.

## Required verification after every change

After each change, including documentation or configuration edits, run these commands from the repository root in this order:

```sh
task taskotter:ci:fix
task taskotter:ci
```

Both commands must finish successfully before reporting completion. Inspect changes produced by the fix task. Resolve failures and rerun both commands after further edits until the final tree passes. If an external blocker prevents a successful run, report the exact blocker and failed command; keep the task incomplete rather than claiming success or skipping checks.

## Repository inspection

Prefer lean-ctx for repository work: `ctx_read` for file reads, `ctx_search` for code searches, and `ctx_shell` or `/opt/homebrew/bin/lean-ctx -c "<command>"` for shell commands. Read `/Users/mostafakhairy/.codex/LEAN-CTX.md` when you need the full routing rules.
