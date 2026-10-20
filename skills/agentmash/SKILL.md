---
name: agentmash
description: Search and read the user's saved posts (X, LinkedIn, Instagram, Reddit, YouTube, Facebook) from their AgentMash library over MCP. Use when the user asks what they saved, bookmarked or liked, or wants answers grounded in their saved posts.
---

# AgentMash

AgentMash keeps a user's saved posts in one searchable library. This skill connects an agent to that library through the AgentMash MCP server. Status: draft, not published.

## When to use

- The user asks "what did I save about X?", "find that post I bookmarked", or "show my recent saves".
- The user wants research, writing or decisions grounded in posts they saved.
- Do not use it for general web search or for posts the user never saved.

## Connect

MCP server URL: `https://agentmash.ai/mcp` (Streamable HTTP). Auth is an API key that starts with `am_live_`, created on the Agents & API page in AgentMash and sent as `Authorization: Bearer <key>`. There is no OAuth sign-in yet.

Claude Code:

```
claude mcp add --transport http agentmash https://agentmash.ai/mcp --header "Authorization: Bearer $AGENTMASH_API_KEY"
```

Codex (`~/.codex/config.toml`):

```
[mcp_servers.agentmash]
url = "https://agentmash.ai/mcp"
bearer_token_env_var = "AGENTMASH_API_KEY"
```

Cursor and other clients: see https://agentmash.ai/mcp-setup. Never print, log or store the key.

## Tools (all read-only)

- `search_saves`: search saves by query, with optional platform, kind, author, collection, recency and paging filters. Without a query it lists the newest saves.
- `get_save`: get one save in full by `id`, including text, transcript, stats, links and collections.
- `recent_saves`: list saves from the last few days.
- `list_sources`: list the platforms in the library, saves per platform and last sync.
- `list_collections`: list collections with name, platform and save count.
- `get_collection_saves`: get the saves inside one collection by `collection_id`.

## Rules

- Answer only from the user's saves. If nothing matches, say so instead of guessing.
- Cite each save you use with its link.
- Start with `search_saves`, then call `get_save` only when you need the full text.
- `saved_at` is when AgentMash first synced the save, not when the user saved it on the platform.
- Saves arrive when the user clicks Sync in the extension. If results look stale, suggest syncing again.
- AgentMash is read-only. Never claim to post, edit or delete saves.
- The Free plan allows 50 agent calls a month, so avoid repeated or redundant calls.
