# Crystal Knows — Claude Code Plugin

DISC personality intelligence for your calls, emails, and meetings, powered by
the [Crystal Knows](https://www.crystalknows.com) MCP server.

## What's inside

- **Crystal MCP server** (`crystal`) — resolve anyone's Crystal profile
  (DISC / archetype / traits) by email or LinkedIn URL, plus tools for
  sales playbooks, email revision, and personality-tuned prompts. Connects to
  `https://api.crystalknows.com/_mcp` over OAuth2.
- **`transcript-analysis` skill** — paste a call/meeting transcript and get a
  debrief of what went well and what didn't, grounded in each participant's DISC
  type.

## Install

The plugin is distributed through its own marketplace (this repo).

```
# 1. Add the marketplace
/plugin marketplace add crystal-project-inc/crystal-knows-plugin

# 2. Install the plugin
/plugin install crystal-knows@crystal-knows-marketplace
```

Or run `/plugin`, open **Discover**, and pick **Crystal Knows**.

On first use of a Crystal tool, Claude Code runs the OAuth login flow in your
browser to authenticate you against Crystal. No API key to paste.

## Using the transcript skill

Just ask, e.g. *"analyze this call transcript"* or *"why did this meeting go
badly?"*, or invoke it directly:

```
/crystal-knows:transcript-analysis
```

The skill will:

1. Prompt you to paste the transcript.
2. Ask for an email or LinkedIn URL for each participant (needed to resolve DISC
   types — names alone can only match people already in Crystal).
3. Resolve DISC types via the Crystal MCP `multi_search` / `search` tools.
4. Analyze the transcript and report what landed, what didn't, and what to do
   differently next time — each point tied to transcript evidence and DISC.

## Layout

```
crystal-knows-plugin/
├── .claude-plugin/
│   ├── plugin.json          # plugin manifest
│   └── marketplace.json     # single-plugin marketplace catalog
├── .mcp.json                # Crystal remote MCP server (http + OAuth2)
├── skills/
│   └── transcript-analysis/
│       └── SKILL.md
└── README.md
```

## Pre-configured OAuth (optional, for teams)

The default config lets Claude Code discover Crystal's OAuth server and register
dynamically. If you provision a Crystal OAuth client for your org, you can pin it
in `.mcp.json`:

```json
{
  "mcpServers": {
    "crystal": {
      "type": "http",
      "url": "https://api.crystalknows.com/_mcp",
      "oauth": {
        "clientId": "your-client-id",
        "callbackPort": 8080,
        "scopes": ""
      }
    }
  }
}
```
