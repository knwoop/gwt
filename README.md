# giwo - Git WorkTree Manager

A CLI tool for efficiently managing Git worktrees. Supports parallel work across multiple branches and manages the entire lifecycle of worktrees.

## Installation

```bash
go install github.com/knwoop/giwo@latest
```

## Commands

### `giwo create <branch-name>`

Create a new worktree based on the default branch.

```bash
giwo create feature-auth
giwo create bugfix-login --base develop
giwo create experiment-ui --force
```

**Options:**
- `--base <branch>` - Base branch to create worktree from (default: repository default branch)
- `--force` - Force creation even if directory exists

**Features:**
- Places worktree in `.worktree/<branch-name>`
- Automatically creates and switches to new branch
- Copies config files (.env, .gitignore, .editorconfig, etc.)
- Fetches default branch via GitHub API (requires GITHUB_TOKEN)

### `giwo remove <branch-name>`

Remove a worktree and optionally its local branch.

```bash
giwo remove feature-auth
giwo remove bugfix-login --keep-branch
giwo remove old-feature --force
```

**Aliases:** `rm`, `delete`

**Options:**
- `--force` - Force removal without confirmation
- `--keep-branch` - Keep the local branch after removing worktree

### `giwo list`

Display all worktrees with status information.

```bash
giwo list
giwo list --verbose
giwo list --format json
```

**Aliases:** `ls`

**Options:**
- `--verbose` - Show detailed information (commits, changes, etc.)
- `--format <table|json|simple>` - Output format

### `giwo status`

Show worktree statistics and recommendations.

```bash
giwo status
```

**Output:**
- Total worktrees count
- Active vs dirty worktrees
- Merged branches that can be cleaned
- Recommended actions

### `giwo clean`

Batch remove worktrees for merged branches.

```bash
giwo clean
giwo clean --dry-run
giwo clean --force
```

**Options:**
- `--dry-run` - Show what would be removed without actually removing
- `--force` - Force removal without confirmation

**Features:**
- Automatically detects merged branches
- Excludes main/master/develop branches
- Shows branch status before removal

### `giwo switch [filter]`

Switch to a worktree interactively with fuzzy search support.

```bash
giwo switch
giwo switch feature
giwo switch --fuzzy
giwo switch --filter auth
giwo switch --print
```

**Aliases:** `sw`

**Options:**
- `--fuzzy` - Use interactive fuzzy search (like fzf)
- `--filter <text>` - Filter worktrees by branch name
- `--print` - Print the selected worktree path instead of switching

**Features:**
- Interactive selection with numbered options
- Fuzzy search with real-time filtering
- Visual status indicators (clean/dirty, ahead/behind)
- Shell integration support

### `giwo prune`

Remove administrative files for orphaned worktrees.

```bash
giwo prune
```

Wrapper around `git worktree prune -v`.

## GitHub Integration

Set `GITHUB_TOKEN` environment variable to enable:
- Automatic default branch detection
- Better API rate limits

```bash
export GITHUB_TOKEN=your_token_here
```

## Directory Structure

```
project-root/
├── .worktree/
│   ├── feature-auth/     # feature-auth branch
│   ├── bugfix-login/     # bugfix-login branch
│   └── experiment-ui/    # experiment-ui branch
└── main-worktree/        # Main worktree
```

## Shell Integration

For seamless directory switching, source the provided shell script:

```bash
# Add to your .bashrc or .zshrc
source /path/to/giwo/scripts/giwo-switch.sh

# Now you can use:
gws                    # Interactive switch
gwf                    # Fuzzy search
giwo-switch --filter ui # Filter and switch
```

## Examples

```bash
# Create a new feature branch worktree
giwo create feature-auth

# Switch to a worktree interactively
giwo switch

# Use fuzzy search to find and switch
giwo switch --fuzzy

# Filter worktrees and switch
giwo switch auth

# List all worktrees
giwo list --verbose

# Check status and get recommendations
giwo status

# Clean up merged branches
giwo clean --dry-run
giwo clean

# Remove specific worktree
giwo remove feature-auth
```
