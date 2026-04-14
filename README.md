# listen2post

Generates Yoshiori-style Hatena blog posts from LISTEN voice diary episodes.

## Setup

### Prerequisites

- [Claude Code](https://claude.com/claude-code)
- [1Password CLI (`op`)](https://developer.1password.com/docs/cli/), signed in
- [mise](https://mise.jdx.dev/)
- Node.js (`npx` must be available)

### LISTEN API token

The LISTEN API token is stored in 1Password at `Private > Listen-api-read`. `mise.toml` only contains a reference resolved via `op read`, so the raw token is never committed.

### Obsidian vault

Generated blog posts are written to `${OBSIDIAN_VAULT}/Notes/listen2post/YYYY-MM-DD-<episode title>.md`. `OBSIDIAN_VAULT` is set in `mise.toml` — edit it to match your vault's absolute path. The filename uses the LISTEN episode title (not the post title) so it stays stable if you edit the post heading later.

### Launch

```sh
mise trust   # first time only
claude
```

mise loads the env vars on directory entry (resolving the 1Password reference via `op read`), and `.mcp.json` picks up `LISTEN_API_TOKEN` for the listen MCP server. Since the shell is never wrapped, the Claude Code TUI works normally.

## Usage

Inside Claude Code, run `/blog`. It walks through episode selection → blog post generation, then writes the post into the Obsidian vault. The only chat output after generation is the absolute file path — open it in Obsidian to review and copy.

## Files

- `.claude/skills/blog/SKILL.md` — the `/blog` skill (transformation rules and voice guide)
- `.mcp.json` — project-local MCP config for `listen-mcp-server`
- `mise.toml` — env vars and 1Password references (safe to commit)
- `CLAUDE.md` — project instructions for Claude
