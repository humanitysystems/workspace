# Humanity Systems workspace guide

This workspace root (`humanitysystems/workspace`) federates Humanity Systems repositories via [`workspace.json`](workspace.json).

## Context routing

Do not duplicate child repo guidance here. Route to local docs and source code:

**⚠ Worktree isolation always applies.** Even if a child repo's `AGENTS.md` does not mention worktrees, the hard boundary in this file (below) is in effect. If your cwd is inside `repos/<repo>/` and you need to make code changes, stop and create a worktree first.

- **Child repos**: Read local `AGENTS.md`, `README.md`, or `skills/` before working inside any child directory under `repos/` (e.g. `repos/humanitypedia`, `repos/warrant`).
- **Workspace CLI**: Use `wspace check --json` for discovery and `wspace update` for conservative sync.

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

### Worktree isolation (hard boundary)

All feature work must happen in a worktree (`worktrees/<repo>/<feature>`). Never create branches or commit directly inside `repos/<repo>`. This is a hard boundary — if an agent's cwd is inside `repos/<repo>`, it must create a worktree and `cd` into it before making any changes.

When any skill produces code changes that need to be applied to a child repo, the agent must:

1. Check if cwd is inside `repos/<repo>/` — if so, stop.
2. Create a worktree: `wspace worktree add <repo> <feature-slug>` from the workspace root.
3. `cd` into the worktree before making any edits.
4. Never commit or push from inside `repos/<repo>/`.


### Hard boundaries (human approval required)

- Creating or deleting repositories or manifest entries.
- Pushing directly to `main` (resets, force operations).
- Deploying or publishing artifacts.
