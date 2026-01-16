# Homer Setup Guide

## How to Create homer.sh from Ralphy

Since homer.sh is nearly identical to ralphy.sh (just with different branding), here's how to create it:

### Quick Method (Recommended)

```bash
# Clone this repo
git clone https://github.com/TheAuthorityio/homer.git
cd homer

# Download ralphy.sh from the original repo
curl -o homer.sh https://raw.githubusercontent.com/michaelshimeles/ralphy/main/ralphy.sh

# Replace all instances of "ralphy" with "homer" and "Ralphy" with "Homer"
sed -i '' 's/ralphy/homer/g' homer.sh
sed -i '' 's/Ralphy/Homer/g' homer.sh  
sed -i '' 's/RALPHY/HOMER/g' homer.sh

# Update version to 1.0.0
sed -i '' 's/VERSION=".*"/VERSION="1.0.0"/' homer.sh

# Make it executable
chmod +x homer.sh
```

### Adding Homer's Persona

Optionally, you can add Homer-specific messages to make it more fun. Edit `homer.sh` and modify these sections:

#### 1. Banner (around line 1803)

Replace:
```bash
echo "${BOLD}Homer${RESET} - Running until PRD is complete"
```

With:
```bash
echo "${BOLD}Homer${RESET} - D'oh! Working until your PRD is done"
echo "${DIM}(Persistent coding, like me trying to fix things)${RESET}"
```

#### 2. Success Messages (around line 1320)

After successful task completion, you could add:
```bash
log_success "Woo-hoo! Task complete!"
```

#### 3. Error Messages

Replace generic error messages with Homer-isms:
- "D'oh! That didn't work" 
- "Why you little..." (on retries)
- "Mmm... donuts" (on completion)

But honestly, the functional changes are minimal - it's really just:
1. **Rename**: `ralphy` → `homer` everywhere
2. **Branch prefixes**: `ralphy/` → `homer/`  
3. **Version**: Start at 1.0.0
4. **Help text**: Update references

### Manual Changes Needed

If you want to do it manually, here are the exact replacements:

| Original | Replace With |
|----------|-------------|
| `ralphy.sh` | `homer.sh` |
| `ralphy/` (branch prefix) | `homer/` |
| `"Ralphy"` (display name) | `"Homer"` |
| `VERSION="3.1.0"` | `VERSION="1.0.0"` |

### What Stays The Same

The entire core functionality remains identical:
- All command-line flags (`--parallel`, `--branch-per-task`, etc.)
- AI engine support (Claude, OpenCode, Cursor)
- Task sources (Markdown, YAML, GitHub Issues)
- Parallel execution with worktrees
- Retry logic and error handling
- Progress monitoring
- Cost tracking

### Testing

After creating `homer.sh`:

```bash
# Test the help
./homer.sh --help

# Test with dry run
echo "- [ ] Test task" > PRD.md
./homer.sh --dry-run
```

## Philosophy

Homer is functionally identical to Ralphy but with a different personality:

**Ralphy** (from The Simpsons):
- "I'm in danger" 
- A bit chaotic but well-meaning
- Gets confused easily

**Homer** (also from The Simpsons):
- "D'oh!"
- Persistent and doesn't give up
- Simple approach that eventually works
- "Trying is the first step toward failure"

Both get the job done, just with different vibes!

## Contributing

If you have ideas for Homer-specific enhancements:
1. Fork this repo
2. Make your changes
3. Submit a PR

Some fun ideas:
- Add donut break reminders
- Homer-themed progress messages
- Sound effects (if terminal supports it)
- Homer quotes on completion

## Credits

Homer is based on [Ralphy](https://github.com/michaelshimeles/ralphy) by michaelshimeles. All core functionality credit goes to the original author. Homer just adds a different personality layer on top.
