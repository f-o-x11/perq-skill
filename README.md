# Perq skill

An agent skill (plus a remote MCP server) that answers "what free AI credits can I get for X?" with verified offers from [Perq](https://perq.sh): free tiers, signup credits, startup programs, student and research perks, and perk marketplaces for AI APIs, models, GPUs and dev tools.

Every offer comes from the vendor's own page and includes the source URL, a verbatim quote from that page, and a last-verified timestamp. The skill tells the agent to show the source link and date with every offer and never to invent offers or amounts.

It works with Claude Code, Codex, Cursor, Hermes Agent, and any agent that reads `SKILL.md` files ([agentskills.io](https://agentskills.io) format).

- Website: https://perq.sh
- Agent docs: https://perq.sh/agents
- Search API: `GET https://perq.sh/api/search?q=<need>`
- MCP server: `https://perq.sh/mcp` (Streamable HTTP, no auth, read-only)
- llms.txt: https://perq.sh/llms.txt

The skill is one file: [`skills/perq/SKILL.md`](skills/perq/SKILL.md). The repo also has:

- `.claude-plugin/plugin.json` and `marketplace.json`: Claude Code plugin (skill + MCP server)
- `.mcp.json`: the remote MCP server config for clients that read it
- `gemini-extension.json`: Gemini CLI extension (MCP server + skill as context)
- `server.json`: the official MCP Registry entry (`sh.perq/perq`)

## Install

### Any agent (skills.sh)

```bash
npx skills add f-o-x11/perq-skill
```

Add `-g` to install for all projects, or `-a claude-code`, `-a codex`, `-a cursor`, or `-a hermes-agent` to pick the agent.

### Claude Code

As a plugin (installs the skill and the MCP server):

```bash
claude plugin marketplace add f-o-x11/perq-skill
claude plugin install perq@perq
```

MCP server only:

```bash
claude mcp add --transport http perq https://perq.sh/mcp
```

Or as a plain skill:

```bash
mkdir -p ~/.claude/skills/perq && curl -fsSL https://perq.sh/skill.md -o ~/.claude/skills/perq/SKILL.md
```

### Codex

```bash
npx skills add f-o-x11/perq-skill -a codex -g
```

### Cursor

```bash
npx skills add f-o-x11/perq-skill -a cursor
```

Or add the MCP server to `~/.cursor/mcp.json`:

```json
{ "mcpServers": { "perq": { "url": "https://perq.sh/mcp" } } }
```

### Gemini CLI

```bash
gemini extensions install https://github.com/f-o-x11/perq-skill
```

### Hermes Agent

```bash
hermes skills install f-o-x11/perq-skill/skills/perq
```

## What it does

Ask your agent "what free credits can I get for speech-to-text?" The agent runs:

```bash
curl -sS -G "https://perq.sh/api/search" --data-urlencode "q=free speech to text credits" -d limit=5 -d ref=skill
```

and answers with each offer's value, eligibility, claim link, source link and last-verified date. Filters: `category`, `type`, `student=true`, `no_application=true`, `include_closed=true`, `limit`.

MCP tools: `find_credits(need, filters)`, `get_offer(id)`, `list_packs()`, `get_pack(slug)`, `recently_changed(days)`.

`ref=skill` tells Perq that a search came from this skill. It does not change the results.

## License

MIT. Copyright (c) 2026 Avatar 8 LLC.

Perq is operated by Avatar 8 LLC. Contact: ops@avatar33.com. No paid ranking: vendors cannot pay to be listed or to move up.
