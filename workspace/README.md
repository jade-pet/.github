# Workspace orchestrator

`justfile` here is the **workspace-level** dev orchestrator for Jade. It
forwards to each repo's own justfile and adds cross-repo helpers (`just dev`,
`just test`, …). It runs from the **workspace root**: the directory that holds
the `jade-*` repos and this `.github` checkout.

It lives here (in the org `.github` repo) so it's the single, tracked source of
truth — the per-repo justfiles stay each repo's own concern.

## Setup

After cloning the `jade-*` repos and this `.github` repo into a shared
parent directory, run the bootstrap script from the workspace root:

```sh
cd <workspace-root>   # the dir holding jade-backend, jade-ios, .github, …
./.github/workspace/bootstrap.sh
```

It writes the small shim `justfile` at the workspace root that imports this one.
It's idempotent (re-running is a no-op) and backs up any pre-existing root
`justfile` before overwriting. Or do the same by hand:

```sh
printf "import '.github/workspace/justfile'\n" > justfile
```

The root shim is local-only (the workspace root isn't a git repo); the recipes
it imports — and the bootstrap script — are tracked here.

## Recipes

| Recipe | What |
|---|---|
| `just dev` | Start the backend in the background + launch the iOS app — one command |
| `just stop` | Stop the background dev backend |
| `just test` | Run the backend + iOS test suites |
| `just backend <r>` | Forward to `jade-backend`'s justfile, e.g. `just backend migrate` |
| `just ios <r>` | Forward to `jade-ios`'s justfile, e.g. `just ios run` |
| `just docs <r>` | Forward to `jade-docs`'s justfile, e.g. `just docs serve` |
| `just mac <r>` | Forward to `jade-macos`'s justfile, e.g. `just mac run` (renamed from `desktop`: the old desktop-pet prototype is retired and `jade-macos` is the real Mac app) |

The AI steps in the iOS app need the dev backend; `just dev` starts it for you,
or run `just backend serve` yourself. The app targets `http://127.0.0.1:8000`
(IPv4) so the simulator doesn't hit an IPv6 listener on the same port.
