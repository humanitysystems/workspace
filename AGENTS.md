# Humanity Systems workspace guide

This workspace root (`humanitysystems/workspace`) federates Humanity Systems repositories via [`workspace.json`](workspace.json).

## Context routing

Do not duplicate child repo guidance here. Route to local docs and source code:

- **Child repos**: Read local `AGENTS.md`, `README.md`, or `skills/` before working inside any child directory under `repos/` (e.g. `repos/humanitypedia`, `repos/warrant`).
- **Workspace CLI**: Use `works check --json` for discovery and `works update` for conservative sync.

## Core rules

### Manifest membership

- [`workspace.json`](workspace.json) is the single source of truth for allowlisted repositories checked out under `repos/`.

### Feature worktrees

- Develop features in parallel worktrees using absolute workspace root paths:
  ```sh
  git -C repos/<repo> worktree add "$PWD/worktrees/<repo>/<feature-slug>" -b <feature-slug>
  ```
- Clean up when finished:
  ```sh
  git -C repos/<repo> worktree remove "$PWD/worktrees/<repo>/<feature-slug>"
  git -C repos/<repo> worktree prune
  ```

### Local secrets

- Never edit or commit `.env` files in `repos/` or `worktrees/`.
- Central `secrets/<repo>/` vault at the workspace root is the single source of truth for local credentials.

### Hard boundaries (human approval required)

- Creating or deleting repositories or manifest entries.
- Pushing directly to `main` (resets, force operations).
- Deploying or publishing artifacts.
