# Homer

An autonomous AI coding loop that keeps trying until your PRD is done. Like Homer trying to fix stuff - persistent, gets the job done eventually, and maybe takes a donut break.

## What It Does

1. Reads tasks from a PRD file, YAML file, or GitHub Issues
2. Sends each task to an AI assistant
3. The AI implements the feature, writes tests, and commits changes
4. Repeats until all tasks are done (or you tell it to stop)

Think of it like Homer at the power plant - he keeps pressing buttons until something works.

## Quick Start

```bash
# Clone the repo
git clone https://github.com/yourusername/homer.git
cd homer
chmod +x homer.sh

# Create a PRD file with tasks
cat > PRD.md << 'EOF'
# My Project

## Tasks

- [ ] Create user authentication
- [ ] Add dashboard page
- [ ] Build API endpoints
EOF

# Run Homer
./homer.sh
```

Mmm... automated coding.

## Requirements

**Required:**
- One of: [Claude Code CLI](https://github.com/anthropics/claude-code), [OpenCode CLI](https://opencode.ai/docs/), or [Cursor](https://cursor.com/) (with `agent` in PATH)
- `jq` (for JSON parsing)

**Optional:**
- `yq` - only if using YAML task files
- `gh` - only if using GitHub Issues or `--create-pr`
- `bc` - for cost calculation

## Task Sources

### Markdown (default)
```bash
./homer.sh --prd PRD.md
```

Format your PRD like this:
```markdown
## Tasks

- [ ] First task
- [ ] Second task  
- [x] Completed task (will be skipped)
```

### YAML
```bash
./homer.sh --yaml tasks.yaml
```

Format:
```yaml
tasks:
  - title: First task
    completed: false
  - title: Second task
    completed: false
```

### GitHub Issues
```bash
./homer.sh --github owner/repo
./homer.sh --github owner/repo --github-label "ready"
```

Uses open issues from the repo. Issues are closed automatically when done.

## Parallel Mode

Run multiple AI agents simultaneously, each in its own isolated git worktree:

```bash
./homer.sh --parallel  # 3 agents (default)
./homer.sh --parallel --max-parallel 5  # 5 agents
```

### How It Works

Each agent gets:
- Its own git worktree (separate directory)
- Its own branch (`homer/agent-1-task-name`, `homer/agent-2-task-name`, etc.)
- Complete isolation from other agents

```
Agent 1 ─► worktree: /tmp/xxx/agent-1 ─► branch: homer/agent-1-create-user-model
Agent 2 ─► worktree: /tmp/xxx/agent-2 ─► branch: homer/agent-2-add-api-endpoints
Agent 3 ─► worktree: /tmp/xxx/agent-3 ─► branch: homer/agent-3-setup-database
```

### After Completion

**Without `--create-pr`:** Branches are automatically merged back to your base branch. If there are merge conflicts, AI will attempt to resolve them.

**With `--create-pr`:** Each completed task gets its own pull request. Branches are kept for review.

```bash
./homer.sh --parallel --create-pr  # Create PRs for each task
./homer.sh --parallel --create-pr --draft-pr  # Create draft PRs
```

## Branch Workflow

Create a separate branch for each task:

```bash
./homer.sh --branch-per-task  # Create feature branches
./homer.sh --branch-per-task --base-branch main  # Branch from main
./homer.sh --branch-per-task --create-pr  # Create PRs automatically
./homer.sh --branch-per-task --create-pr --draft-pr  # Create draft PRs
```

Branch naming: `homer/<slug-of-task-title>`

Example: "Add user authentication" becomes `homer/add-user-authentication`

## AI Engine

```bash
./homer.sh  # Claude Code (default)
./homer.sh --opencode  # OpenCode
./homer.sh --cursor  # Cursor agent
```

### Engine Details

| Engine | CLI Command | Permissions Flag | Output |
|--------|-------------|------------------|--------|
| Claude Code | `claude` | `--dangerously-skip-permissions` | Token usage + cost estimate |
| OpenCode | `opencode` | `OPENCODE_PERMISSION='{"*":"allow"}'` | Token usage + actual cost |
| Cursor | `agent` | `--force` | API duration (no token counts) |

**Note:** Cursor's CLI doesn't expose token usage, so Homer tracks total API duration instead.

## All Options

### AI Engine

| Flag | Description |
|------|-------------|
| `--claude` | Use Claude Code (default) |
| `--opencode` | Use OpenCode |
| `--cursor`, `--agent` | Use Cursor agent |

### Task Source

| Flag | Description |
|------|-------------|
| `--prd FILE` | PRD file path (default: PRD.md) |
| `--yaml FILE` | Use YAML task file |
| `--github REPO` | Fetch from GitHub issues (owner/repo) |
| `--github-label TAG` | Filter GitHub issues by label |

### Parallel Execution

| Flag | Description |
|------|-------------|
| `--parallel` | Run tasks in parallel |
| `--max-parallel N` | Max concurrent agents (default: 3) |

### Git Branches

| Flag | Description |
|------|-------------|
| `--branch-per-task` | Create a branch for each task |
| `--base-branch NAME` | Base branch (default: current branch) |
| `--create-pr` | Create pull requests |
| `--draft-pr` | Create PRs as drafts |

### Workflow

| Flag | Description |
|------|-------------|
| `--no-tests` | Skip tests |
| `--no-lint` | Skip linting |
| `--fast` | Skip both tests and linting |

### Execution Control

| Flag | Description |
|------|-------------|
| `--max-iterations N` | Stop after N tasks (0 = unlimited) |
| `--max-retries N` | Retries per task on failure (default: 3) |
| `--retry-delay N` | Seconds between retries (default: 5) |
| `--dry-run` | Preview without executing |

### Other

| Flag | Description |
|------|-------------|
| `-v, --verbose` | Debug output |
| `-h, --help` | Show help |
| `--version` | Show version |

## Examples

```bash
# Basic usage
./homer.sh

# Fast mode with OpenCode
./homer.sh --opencode --fast

# Use Cursor agent
./homer.sh --cursor

# Cursor with parallel execution  
./homer.sh --cursor --parallel --max-parallel 4

# Parallel with 4 agents and auto-PRs
./homer.sh --parallel --max-parallel 4 --create-pr

# GitHub issues with parallel execution
./homer.sh --github myorg/myrepo --parallel

# Feature branch workflow
./homer.sh --branch-per-task --create-pr --base-branch main

# Limited iterations with draft PRs
./homer.sh --max-iterations 5 --branch-per-task --create-pr --draft-pr

# Preview what would happen
./homer.sh --dry-run --verbose
```

## Homer's Coding Philosophy

Homer approaches coding like he approaches life:
- **Simple is better**: Working code beats elegant code
- **Keep trying**: If it doesn't work, try again (with retries)
- **Take breaks**: Progress monitoring so you know when to grab a snack
- **Ask for help**: Multiple AI assistants working together
- **Fix mistakes**: Automatic merge conflict resolution

D'oh! Found a bug? Just let Homer keep trying.

## Progress Display

While running, you'll see:
- A spinner with the current step (Thinking, Reading, Implementing, Testing, Committing)
- The current task name
- Elapsed time

In parallel mode:
- Number of agents setting up, running, done, and failed
- Final results with branch names
- Error logs for any failed agents

## Cost Tracking

At completion, Homer shows different metrics depending on the AI engine:

| Engine | Metrics Shown |
|--------|---------------|
| Claude Code | Input/output tokens, estimated cost |
| OpenCode | Input/output tokens, actual cost |
| Cursor | Total API duration (tokens not available) |

All engines show branches created (if using `--branch-per-task`).

## Why "Homer"?

Because like Homer Simpson:
- He's persistent (keeps running until done)
- He's simple (straightforward bash script)
- He gets results (eventually)
- He learns from mistakes (retry logic)
- He works well with others (parallel mode)

And most importantly: "Trying is the first step toward failure." - Homer Simpson

## License

MIT

## Credits

Inspired by [Ralphy](https://github.com/michaelshimeles/ralphy) by michaelshimeles. Homer is a friendly fork with a different personality.
