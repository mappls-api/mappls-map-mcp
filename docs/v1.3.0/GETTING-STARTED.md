[<img src="https://about.mappls.com/images/mappls-b-logo.svg" height="60"/>](https://www.mapmyindia.com/api)

[Home](../../README.md) · [Overview](README.md) · [Explainer](EXPLAINER.md) · [Getting Started](GETTING-STARTED.md) · [Tools](TOOLS.md) · [Use Cases](USE-CASES.md)

# Getting Started

**Connect an MCP client to the Mappls MCP Server**

## Overview

This page shows how to connect an MCP client to the Mappls MCP Server: Google Antigravity, Claude Desktop, Claude Code, Cursor and others. The server gives the model **six tools that each answer a driver's question in one call**, and it resolves categories, place names and coordinates itself, so the client never has to look them up first.

The six tools are `mappls_find_places` (find places by type or name), `mappls_plan_route` (plan a drive, with stops), `mappls_find_along_route` (find something on the way), `mappls_trip_info` (arrival time, cost, hazards), `mappls_place_lookup` (details about one place) and `mappls_where_am_i` (where the car is). Each is described in the [Tool Reference](TOOLS.md).

## Connection Details

| Parameter | Value |
|---|---|
| **URL** | `https://geoai.mappls.com/agent/core/mcp` |
| **Transport** | Streamable HTTP. The legacy HTTP+SSE transport is not offered. |
| **Protocol** | JSON-RPC 2.0. MCP versions 2025-03-26 and 2025-11-25 were both negotiated in testing. |
| **Auth header** | `access_token: YOUR_REST_KEY` (a plain Mappls REST key, with no `Bearer` prefix) |
| **Session** | Stateful. The server returns an `mcp-session-id` that every later request sends back. |
| **Tools** | 6 (the raw API tools exist on the server but are hidden from the model) |

Get your `access_token` (REST key) from the Mappls developer portal or from the server admin. Send it in the header, never in the URL. A key is stored in plain text in several client config files below, so keep those files out of version control.

## Quick Verification (curl)

Run these before setting up a client. Each was run against the server URL above on 10 November 2026.

### 1. Health, no key needed

```bash
curl -s https://geoai.mappls.com/health
```

Expected (`sessions_active` and `uptime_seconds` change):

```json
{
  "status": "ok",
  "name": "mappls-mcp-server",
  "description": "MCP server exposing Mappls geospatial APIs as callable tools for LLM agents",
  "version": "1.5.2+token-latency-optimization.v1.3.0",
  "transport": "http",
  "sessions_active": 24,
  "uptime_seconds": 1351
}
```

### 2. Open a session, list the tools, make a first call

This needs `curl` and `jq`.

```bash
KEY=YOUR_REST_KEY
URL=https://geoai.mappls.com/agent/core/mcp
H=(-H "Content-Type: application/json"
   -H "Accept: application/json, text/event-stream"
   -H "access_token: $KEY")

# initialize: keep the session id from the response headers
SID=$(curl -s -D - -o /dev/null -X POST "$URL" "${H[@]}" -d '{
    "jsonrpc": "2.0", "id": 1, "method": "initialize",
    "params": {"protocolVersion": "2025-03-26", "capabilities": {},
               "clientInfo": {"name": "test", "version": "1.0"}}}' \
  | awk -F': ' 'tolower($1)=="mcp-session-id"{print $2}' | tr -d '\r')

# tell the server the client is ready (expect HTTP 202)
curl -s -o /dev/null -w "%{http_code}\n" -X POST "$URL" "${H[@]}" \
  -H "mcp-session-id: $SID" \
  -d '{"jsonrpc": "2.0", "method": "notifications/initialized"}'

# list the tools
curl -s -X POST "$URL" "${H[@]}" -H "mcp-session-id: $SID" \
  -d '{"jsonrpc": "2.0", "id": 2, "method": "tools/list"}' \
  | sed -n 's/^data: //p' | jq -r '.result.tools[].name'

# first tool call: petrol pumps near a named place
curl -s -X POST "$URL" "${H[@]}" -H "mcp-session-id: $SID" \
  -d '{"jsonrpc": "2.0", "id": 3, "method": "tools/call",
       "params": {"name": "mappls_find_places",
                  "arguments": {"what": "petrol pump", "near": "Connaught Place",
                                "said": "Find a petrol pump near Connaught Place"}}}' \
  | sed -n 's/^data: //p' | jq -r '.result.content[0].text'
```

Expected: `202`, then the six tool names

```text
mappls_find_places
mappls_plan_route
mappls_find_along_route
mappls_trip_info
mappls_place_lookup
mappls_where_am_i
```

and a JSON answer whose `summary` is `Found 10 result(s) for 'petrol pump' near Connaught Place, New Delhi.`, with the ten places in `data.results`.

## Client Setup

Every client needs the same three things: the URL, the header `access_token: YOUR_REST_KEY`, and a restart or reload so the tools appear.

### Claude Desktop

Claude Desktop reaches HTTP servers through a local bridge, `mcp-remote`. It needs Node.js (`node --version`).

**Config file:** Linux `~/.config/Claude/claude_desktop_config.json`, macOS `~/Library/Application Support/Claude/claude_desktop_config.json`, Windows `%APPDATA%\Claude\claude_desktop_config.json`.

```json
{
  "mcpServers": {
    "mappls": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://geoai.mappls.com/agent/core/mcp",
        "--header",
        "access_token:YOUR_REST_KEY"
      ]
    }
  }
}
```

If `npx` is not on the PATH, use its full path (`which npx`). Restart Claude Desktop, and the six tools appear in the tools panel.

### Claude Code (CLI)

```bash
claude mcp add --transport http mappls https://geoai.mappls.com/agent/core/mcp \
  --header "access_token: YOUR_REST_KEY"
claude mcp list
```

Flags go before the name and URL. The default scope is this project, for you only (stored in `~/.claude.json`). Add `--scope user` for every project. To share it with a team, use a project file, `.mcp.json` in the project root, and keep the key in an environment variable:

```json
{
  "mcpServers": {
    "mappls": {
      "type": "http",
      "url": "https://geoai.mappls.com/agent/core/mcp",
      "headers": { "access_token": "${MAPPLS_REST_KEY}" }
    }
  }
}
```

### Cursor

Cursor connects to HTTP servers directly. **Config file:** `.cursor/mcp.json` in the project, or `~/.cursor/mcp.json` for every project (also **Cursor Settings → MCP**).

```json
{
  "mcpServers": {
    "mappls": {
      "url": "https://geoai.mappls.com/agent/core/mcp",
      "headers": { "access_token": "${env:MAPPLS_REST_KEY}" }
    }
  }
}
```

Cursor expands `${env:NAME}` in `url` and `headers`, so the key can stay in your shell environment. Restart Cursor after saving, and the tools appear under the MCP section of the chat panel.

### Google Antigravity

Antigravity connects to remote MCP servers over **Streamable HTTP only** and sends any headers you list, so the Mappls `access_token` header works directly, with no bridge.

**1. Open the config file.** Use the UI path, because it opens the file your version reads.

| Surface | How |
|---|---|
| **IDE** | **…** at the top of the agent side panel → **MCP Servers** → **Manage MCP Servers** → **View raw config** |
| **CLI** | Type `/mcp` in the prompt panel to open the MCP Manager, or edit the files directly |
| **App** | **Customizations** → **Installed** → in the **MCP Servers** header, **Copy Config File Path** |

The files are `~/.gemini/config/mcp_config.json` (every project) and `<workspace>/.agents/mcp_config.json` (this workspace only). Some older guides name `~/.gemini/antigravity/mcp_config.json`.

**2. Add the server.**

```json
{
  "mcpServers": {
    "mappls": {
      "serverUrl": "https://geoai.mappls.com/agent/core/mcp",
      "headers": {
        "access_token": "YOUR_REST_KEY"
      }
    }
  }
}
```

| Field | Meaning |
|---|---|
| `serverUrl` | The endpoint of a remote Streamable HTTP server (`url` also works). Required for a remote server. |
| `headers` | Custom HTTP headers sent with every request. Default `{}`. |
| `timeoutSeconds` | Optional. Replaces the defaults of 180 s per tool call and 300 s to connect. `-1` turns the limits off. |
| `disabled` | Optional. `true` suspends the connection but keeps the entry. |

**3. Check it.** Save, then restart the server from the MCP manager if the tools do not appear. The `mappls` server should list its six tools. Try prompts that name the places:

- "Find petrol pumps near Connaught Place."
- "Plan a drive from India Gate to Cyber Hub and tell me when I will arrive."
- "Is the ICICI Bank in Okhla good, and what are its working hours?"

> **Warning:** `mcp_config.json` holds the key in plain text. Prefer the global file (`~/.gemini/config/`). If you use the workspace file, add `.agents/` to `.gitignore`. The legacy HTTP+SSE transport and the `httpUrl` field are not supported by Antigravity.

### Any Other MCP Client

Use the URL, the transport "Streamable HTTP" (sometimes called "HTTP" or "remote"), and a custom header `access_token`. If the client only speaks stdio, bridge it as in the Claude Desktop section.

## Session Management

The server runs in **stateful mode**, so every client session needs a session id.

| Step | What to do |
|---|---|
| 1 | Call `initialize`. The server returns `mcp-session-id` in the response headers. |
| 2 | Send `notifications/initialized`, then send the `mcp-session-id` header on every later request. |
| 3 | The session ends after 30 minutes idle or 2 hours in total. |
| 4 | Call `initialize` again for a new session id. The server's memory of the old one is gone. |

MCP-aware clients (Claude Desktop, Claude Code, Cursor, Antigravity) do this automatically. A raw HTTP client manages the id itself, as the curl example shows.

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| `401` or "invalid access_token" | The key is wrong or not enabled for this service. It can also happen once when the server's own key check times out. | Check the key, and retry once |
| `[INVALID_INPUT] No car position for near …` | The request said "near me" and the client gave no location | Name the place, or give `lat,lng` |
| `No trip given` or `No destination given and no trip planned in this session` | A follow-up with no trip in this session | Call `mappls_plan_route` first, or pass `to` |
| `'number 2' refers to the last results …` | The list is older than 30 minutes, or this is a new session | Search again |
| `[BACKEND_ERROR] … Try again in a few seconds` | A Mappls or network hiccup | Retry |
| Only six tools are listed | Expected: the raw API tools are hidden from the model | Nothing to fix |
| Tools do not appear in the client | The client was not restarted, or it needs "Streamable HTTP" selected | Restart or reload, and check the transport setting |
| Requests with an old `mcp-session-id` are refused | The session expired | Call `initialize` again |

## Client Documentation

- Google Antigravity: [antigravity.google/docs/mcp](https://antigravity.google/docs/mcp)
- Claude Code: [code.claude.com/docs/en/mcp](https://code.claude.com/docs/en/mcp)
- Cursor: [cursor.com/docs/context/mcp](https://cursor.com/docs/context/mcp)

---

For any queries and support, please contact:

[<img src="https://about.mappls.com/images/mappls-logo.svg" height="40"/>](https://about.mappls.com/api/)

Need support? [Contact us](https://about.mappls.com/contact/)

<div align="center">© Copyright 2026 CE Info Systems Ltd. All Rights Reserved.</div>

<div align="center">
  <a href="https://about.mappls.com/api/terms-&-conditions">Terms & Conditions</a> |
  <a href="https://about.mappls.com/about/privacy-policy">Privacy Policy</a>
</div>
