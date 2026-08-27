# Humanity Systems workspace

Multi-repo workspace for [Humanity Systems](https://github.com/humanitysystems),
powered by [`works`](https://github.com/wazootech/workspace-cli) — a git-native
workspace CLI. This repository holds the manifest and working rules; the actual
checkouts are cloned (never committed) into `repos/`.

## Repositories in this context

| Repo | Role | Group |
|---|---|---|
| [humanitypedia](https://github.com/humanitysystems/humanitypedia) | Company brain | `company-brain` |
| [warrant](https://github.com/humanitysystems/warrant) | Flagship project — local, transparent MCP proxy | `flagship` |

## Layout

```
humanitysystems/
├── workspace.json    # Manifest: the single source of truth for repo membership
├── repos/            # Canonical checkouts (gitignored, managed by works)
├── worktrees/        # Feature worktrees (gitignored, one branch per directory)
└── secrets/          # Local env vault (gitignored; source for works env sync)
```

## Setup

Requires [Deno](https://deno.com) 2+.

```bash
deno install -g --name works jsr:@wazoo/workspace
works init   # clone any missing repos from the manifest
works check  # verify everything is clean
```

## Daily workflow

```bash
works check                       # read-only baseline: CLEAN / DIRTY / DIVERGED ...
works update                      # fetch + fast-forward clean default branches only
works worktree add warrant feat   # feature work under worktrees/warrant/feat/
cd worktrees/warrant/feat          # develop here, never on the canonical checkout
git push -u origin feat && gh pr create
works worktree list --stale       # find merged branches after PRs land
works worktree remove warrant feat
works env sync                    # copy secrets/secrets/* into checkouts & worktrees
```

`works update` is conservative by design: it never resets, rebases, stashes,
or rewrites history, and it skips dirty repositories and feature branches.
