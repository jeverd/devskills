# Graphite CLI cheat sheet

Quick reference for `gt` commands used in PR stacking workflows.

## Setup

```bash
command -v gt              # Verify Graphite CLI is installed
gt version                 # Check installed version (need v1.6.7+ for MCP)
gt init                    # Initialize Graphite in a repo (sets trunk branch)
gt submit --stack --dry-run --no-interactive  # Verify repo is synced with Graphite app before real submit
```

If `gt` is missing, install it first (`brew install withgraphite/tap/graphite` or `npm install -g @withgraphite/graphite-cli`). If the dry run says the repo is not synced with Graphite, stop and sync it at https://app.graphite.com/settings/synced-repos before proceeding.

## Creating branches (stack building)

```bash
gt create [name] -am "msg"  # New branch stacked on current, stage all, commit
gt create [name] -m "msg"   # New branch (stage files yourself first)
gt create [name]            # Auto-generated name, opens editor for message
```

Aliases: `gt c` = `gt create`

## Modifying branches

```bash
gt modify -a                # Amend current commit, stage all changes
gt modify -a -m "msg"       # Amend with new message
gt modify --commit -am "msg" # Add a second commit to the current branch
gt modify --into <branch>   # Modify a different branch than current
gt absorb -a                # Absorb changes into downstack branches
```

Aliases: `gt m` = `gt modify`

## Submitting PRs

```bash
gt submit                    # Current branch + all ancestors (downstack)
gt submit --stack            # Current branch + entire stack (up + down). Alias: gt ss
gt submit --draft            # Create PRs as drafts
gt submit --publish          # Convert draft PRs to published
gt submit --merge-when-ready  # Auto-merge when checks pass (alias: -m)
gt submit --restack           # Restack before submitting
gt submit --no-interactive    # Skip all interactive prompts
```

Default: submits current branch and all ancestors down to trunk.

## Navigating the stack

```bash
gt up                       # One branch toward tip (upstack)
gt down                     # One branch toward trunk (downstack)
gt top                      # Jump to tip of stack
gt bottom                   # Jump to bottom (closest to trunk, not trunk itself)
gt checkout                  # Interactive branch picker
gt checkout --trunk          # Jump to trunk (main). Alias: gt checkout -t
gt up 2                     # Move up 2 branches
gt down 3                   # Move down 3 branches
```

## Viewing the stack

```bash
gt log                      # Visual stack graph with branch relationships
gt ls                       # Short list of branches in current stack
```

## Syncing and cleanup

```bash
gt sync                     # Pull trunk changes, rebase stack, prompt to delete merged branches
gt sync --restack           # Also restack branches after sync
gt repo sync --restack      # Restack ALL open PRs after deleting merged branches
gt restack                  # Manually restack (propagate changes upstack)
gt continue                 # Continue after resolving merge conflicts
```

## Deleting branches

```bash
gt delete <branch> -f -q          # Force delete without prompt
gt delete <branch> --downstack    # Delete branch + all ancestors
gt delete <branch> --upstack      # Delete branch + all descendants
```

## MCP setup (AI agents)

```bash
# Claude Code
claude mcp add graphite gt mcp

# Cursor / .mcp.json
{
  "mcpServers": {
    "graphite": {
      "command": "gt",
      "args": ["mcp"]
    }
  }
}
```

Requires Graphite CLI v1.6.7+. MCP enables AI agents to create stacked PRs automatically.

## Common pitfalls

| Problem | Fix |
|---------|-----|
| `git rebase` / `git cherry-pick` | Don't use raw git rebase. Use `gt modify` or `gt restack` instead. |
| `gt create` fails with "not initialized" | Run `gt init` in the repo root first. |
| Merge conflicts during restack | Resolve in editor, then `gt continue`. |
| Stale trunk before starting | Always `gt sync` before `gt create`. |
| Wrong branch order | `gt log` to see current order, `gt checkout` to navigate. |
| Need to insert PR mid-stack | `gt down` to the parent branch, then `gt create new-name`. |