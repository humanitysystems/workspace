# Humanity Systems workspace

Multi-repo workspace for [Humanity Systems](https://github.com/humanitysystems),
powered by [`wspace`](https://github.com/wazootech/workspace-cli) — a git-native
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
├── repos/            # Canonical checkouts (gitignored, managed by wspace)
├── worktrees/        # Feature worktrees (gitignored, one branch per directory)
└── secrets/          # Local env vault (gitignored; source for wspace env sync)
```

## Setup

Requires [Deno](https://deno.com) 2+.

```bash
deno install -g --name wspace jsr:@wazoo/workspace
wspace init   # clone any missing repos from the manifest
wspace check  # verify everything is clean
```

## Daily workflow

```bash
wspace check                       # read-only baseline: CLEAN / DIRTY / DIVERGED ...
wspace update                      # fetch + fast-forward clean default branches only
wspace worktree add warrant feat   # feature work under worktrees/warrant/feat/
cd worktrees/warrant/feat          # develop here, never on the canonical checkout
git push -u origin feat && gh pr create
wspace worktree list --stale       # find merged branches after PRs land
wspace worktree remove warrant feat
wspace env sync                    # copy secrets/secrets/* into checkouts & worktrees
```

`wspace update` is conservative by design: it never resets, rebases, stashes,
or rewrites history, and it skips dirty repositories and feature branches.
