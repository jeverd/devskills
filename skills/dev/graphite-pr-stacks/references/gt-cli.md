# Graphite CLI Quick Reference

Core commands for PR stack workflows. Full docs: https://graphite.com/docs/cli-tutorials

## Setup

```bash
command -v gt            # Verify Graphite CLI is installed
gt --version             # Check version (need v1.6.7+ for MCP)
gt init                  # Initialize repo, select trunk branch
gt submit --stack --dry-run --no-interactive  # Verify repo is synced with Graphite app before real submit
```

If `gt submit --stack --dry-run --no-interactive` says the repo is not synced with Graphite, stop and sync it at https://app.graphite.com/settings/synced-repos before proceeding.

## Branch lifecycle

```bash
gt create [name] -am "msg"   # Create branch on top of current, stage all, commit
gt create -am "msg"          # Auto-generate branch name from message

gt modify -a                  # Amend current commit with all staged changes
gt modify -a -m "new msg"    # Amend with new message
gt modify --commit -am "msg" # Add a second commit (keeps history)

gt delete <branch>            # Delete a branch
gt delete <branch> --upstack  # Delete branch + all descendants
```

## Navigation

```bash
gt up / gt down         # Move one branch up/down the stack
gt top / gt bottom      # Jump to tip/bottom of current stack
gt checkout              # Interactive branch picker
gt checkout <name>       # Jump to a specific branch
gt checkout --trunk      # Jump to trunk (main)
gt log                   # Visual stack graph
gt ls                    # Short branch list
```

## Submitting PRs

```bash
gt submit                       # Current branch + ancestors (downstack)
gt submit --stack  / gt ss     # Current branch + full stack (ancestors + descendants)
gt submit --stack --draft      # Submit as drafts
gt submit --stack --merge-when-ready  # Auto-merge when checks pass
gt submit --restack             # Restack before submitting
gt submit --no-interactive      # Non-interactive (CI-friendly)
```

## Sync & cleanup

```bash
gt sync                      # Pull trunk, rebase stack, delete merged branches
gt repo sync --restack       # Restack all open PRs after merges
gt restack                   # Manually restack current branch
```

## MCP (AI agent integration)

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

## Common patterns

```bash
# Start a new stack from trunk
gt checkout --trunk && gt sync
gt create my-first-pr -am "Add initial feature"

# Add second PR on top
gt create my-second-pr -am "Add endpoint calling the feature"

# Submit whole stack
gt submit --stack

# Address review feedback on mid-stack branch
gt checkout my-first-pr
# ... make changes ...
gt modify -am "Add validation"
gt submit

# After PRs get merged
gt sync
gt repo sync --restack
gt submit --stack
```