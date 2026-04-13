# listen2post

Generates Yoshiori-style Hatena blog posts from LISTEN voice diary episodes.

## Setup

### Prerequisites

- [Claude Code](https://claude.com/claude-code)
- [1Password CLI (`op`)](https://developer.1password.com/docs/cli/)
- Node.js (`npx` must be available)

### LISTEN API token

The LISTEN API token is stored in 1Password at `Private > Listen-api-read`. `.env` only contains a reference (`op://...`), so the raw token is never committed.

### Obsidian vault

Generated blog posts are written to `${OBSIDIAN_VAULT}/Notes/listen2post/YYYY-MM-DD-<episode title>.md`. Set `OBSIDIAN_VAULT` in `.env` to the absolute path of your vault root. The filename uses the LISTEN episode title (not the post title) so it stays stable if you edit the post heading later.

### Launch

```sh
op run --env-file=.env -- claude
```

`op run` resolves the reference at launch and injects `LISTEN_API_TOKEN` into the environment, which `.mcp.json` picks up for the listen MCP server.

## Usage

Inside Claude Code, run `/blog`. It walks through episode selection → blog post generation, then writes the post into the Obsidian vault. The only chat output after generation is the absolute file path — open it in Obsidian to review and copy.

## Files

- `.claude/skills/blog/SKILL.md` — the `/blog` skill (transformation rules and voice guide)
- `.mcp.json` — project-local MCP config for `listen-mcp-server`
- `.env` — 1Password references (safe to commit)
- `CLAUDE.md` — project instructions for Claude
