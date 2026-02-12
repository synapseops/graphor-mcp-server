# Graphor Remote MCP Server

The Graphor Remote MCP Server lets you connect AI assistants and agentic frameworks to the [Graphor](https://graphorlm.com) API using the [Model Context Protocol (MCP)](https://modelcontextprotocol.io). It runs on Cloudflare Workers and uses **OAuth** for authentication.

**Server URL:**

```
https://mcp.graphor.workers.dev/sse
```

> Looking for the local MCP server instead? See the [graphor-mcp](https://www.npmjs.com/package/graphor-mcp) package.

## Quick Start

### Claude.ai

1. Go to **Settings > Connectors > Add custom connector**
2. Fill in the **Name** (e.g. "Graphor")
3. Set the **Remote MCP server URL** to:

```
https://mcp.graphor.workers.dev/sse
```

4. You will be redirected to log in through the OAuth flow
5. Once authenticated, Graphor tools will be available in your conversations

### Claude Desktop

Add the following to your Claude Desktop configuration file (Settings > Developer > Edit Config):

```json
{
  "mcpServers": {
    "graphor_api": {
      "command": "npx",
      "args": ["mcp-remote", "https://mcp.graphor.workers.dev/sse"]
    }
  }
}
```

When you open Claude Desktop, a browser window will open for you to log in. After authenticating, the Graphor tools will appear in the bottom right of your conversation.

### Cursor

Add the following to your Cursor MCP configuration (Settings > Tools & MCP > New MCP Server):

```json
{
  "mcpServers": {
    "graphor_api": {
      "command": "npx",
      "args": ["mcp-remote", "https://mcp.graphor.workers.dev/sse"]
    }
  }
}
```

### Agentic Workflows (LangChain, CrewAI, etc.)

For agentic frameworks that support MCP, connect to the remote server via SSE transport:

**LangChain (Python):**

```python
from langchain_mcp_adapters.client import MultiServerMCPClient

async with MultiServerMCPClient(
    {
        "graphor": {
            "url": "https://mcp.graphor.workers.dev/sse",
            "transport": "sse",
        }
    }
) as client:
    tools = client.get_tools()
    # Use tools with your LangChain agent
```

**LangChain (TypeScript):**

```typescript
import { MultiServerMCPClient } from "@langchain/mcp-adapters";

const client = new MultiServerMCPClient({
  graphor: {
    url: "https://mcp.graphor.workers.dev/sse",
    transport: "sse",
  }
});

const tools = await client.getTools();
// Use tools with your LangChain agent
```

> Note: The OAuth flow will open a browser window on first connection. For headless environments, you may need to complete the OAuth flow beforehand or use [`mcp-remote`](https://www.npmjs.com/package/mcp-remote) as a local proxy.

### Any MCP-compatible Client

For any client that supports remote MCP servers via SSE, use the URL:

```
https://mcp.graphor.workers.dev/sse
```

For clients that only support `stdio` transport, use [`mcp-remote`](https://www.npmjs.com/package/mcp-remote) as a bridge:

```json
{
  "mcpServers": {
    "graphor_api": {
      "command": "npx",
      "args": ["mcp-remote", "https://mcp.graphor.workers.dev/sse"]
    }
  }
}
```

## Troubleshooting

If you run into issues connecting, try the following:

1. **Restart your client** (Claude Desktop, Cursor, etc.)

2. **Test the connection directly** on the command line:

```bash
npx mcp-remote https://mcp.graphor.workers.dev/sse
```

3. **Clear the OAuth cache** if authentication seems stuck:

```bash
rm -rf ~/.mcp-auth
```

## Self-hosting

You can deploy your own instance of this server to Cloudflare Workers.

### One-click deploy

[![Deploy to Cloudflare](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/synapseops/graphor-typescript-sdk/tree/main/packages/mcp-server/cloudflare-worker)

### Manual deploy

1. Clone this repository and install dependencies:

```bash
npm install
```

2. Create the KV namespace:

```bash
npx wrangler@latest kv namespace create remote-mcp-server-oauth-kv
```

3. Add the KV namespace ID to `wrangler.jsonc`

4. Deploy:

```bash
npm run deploy
```

### Local development

```bash
npm install
npm run dev
```

The server will be available at [`http://localhost:8787/`](http://localhost:8787/).

To test with the [MCP Inspector](https://modelcontextprotocol.io/docs/tools/inspector):

1. Run `npx @modelcontextprotocol/inspector`
2. Switch Transport Type to `SSE` and enter `http://localhost:8787/sse`
3. Click "Connect" and log in through the OAuth screen
