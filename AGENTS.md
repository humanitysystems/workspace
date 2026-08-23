# AGENTS.md — Humanity Systems workspace

This directory is a [`wspace`](https://github.com/wazootech/workspace-cli)
workspace. The manifest (`workspace.json`) defines repo membership; canonical
checkouts live in `repos/`, feature work in isolated worktrees under
`worktrees/`.

## Rules

1. **Worktree-first, always.** Never edit a canonical checkout in `repos/`.
   All changes happen inside `worktrees/<repo>/<feature>/`, created with
   `wspace worktree add <repo> <feature>`. This is the only path — not an
   option.
2. **Single-round-trip discovery.** Use `wspace check --json` instead of
   probing each repo with `git status`. One call answers: which repos exist,
   their branch, dirty state, and divergence.
3. **Conservative sync.** `wspace update` only fast-forwards clean default
   branches. It never resets, rebases, stashes, or rewrites history. If
   something is `DIRTY` or on a feature branch, that is intentional — surface
   it to the human instead of "fixing" it.
4. **Baseline branching.** Feature worktrees branch from `origin/<default>`,
   never from local `HEAD`, so features always start from the remote baseline.
5. **Secrets stay local.** Environment files come from the gitignored
   `secrets/` vault via `wspace env sync` (use `--dry-run` first). Never
   commit secrets, never write env files outside checkouts/worktrees.
6. **Exit code contract.** `wspace check` exits 0 when clean, 1 otherwise.
   Pipeline on it; do not interpret "looks fine."

## Lifecycle

```
wspace check
wspace update
wspace worktree add <repo> <feature>
# ...develop in worktrees/<repo>/<feature>, commit...
git push -u origin <feature> && gh pr create
# after merge:
wspace worktree list --stale
wspace worktree remove <repo> <feature>
```

## Hard boundaries (require explicit human approval)

- Deploying or publishing any artifact
- Creating or deleting repositories, including manifest membership changes
- Anything touching `main` directly (pushes, resets, force-operations)

## Adding a repository

Edit `workspace.json` (append to `repositories`), then run `wspace init`.
Never clone repos into `repos/` by hand — unmanaged checkouts are reported as
`UNMANAGED` by `wspace check`.
