# Graphor Remote MCP Server

The Graphor Remote MCP Server lets you connect AI assistants and agentic frameworks to the [Graphor](https://graphorlm.com) API using the [Model Context Protocol (MCP)](https://modelcontextprotocol.io). It runs on Cloudflare Workers and uses **OAuth** for authentication.

**Server URL:**

```
https://mcp.graphor.workers.dev/sse
```

> Looking for the local MCP server instead? See the [Local MCP Server](#local-mcp-server) section below or the [graphor-mcp](https://www.npmjs.com/package/graphor-mcp) package.

## Local MCP Server

For local MCP clients (e.g. Cursor, VS Code), you can use the [`graphor-mcp`](https://www.npmjs.com/package/graphor-mcp) package which authenticates via API key instead of OAuth.

Install directly using the buttons below:

[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/en-US/install-mcp?name=graphor-mcp&config=eyJjb21tYW5kIjoibnB4IiwiYXJncyI6WyIteSIsImdyYXBob3ItbWNwIl0sImVudiI6eyJHUkFQSE9SX0FQSV9LRVkiOiJNeSBBUEkgS2V5In19)
[![Install in VS Code](https://img.shields.io/badge/_-Add_to_VS_Code-blue?style=for-the-badge&logo=data:image/svg%2bxml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIGZpbGw9Im5vbmUiIHZpZXdCb3g9IjAgMCA0MCA0MCI+PHBhdGggZmlsbD0iI0VFRSIgZmlsbC1ydWxlPSJldmVub2RkIiBkPSJNMzAuMjM1IDM5Ljg4NGEyLjQ5MSAyLjQ5MSAwIDAgMS0xLjc4MS0uNzNMMTIuNyAyNC43OGwtMy40NiAyLjYyNC0zLjQwNiAyLjU4MmExLjY2NSAxLjY2NSAwIDAgMS0xLjA4Mi4zMzggMS42NjQgMS42NjQgMCAwIDEtMS4wNDYtLjQzMWwtMi4yLTJhMS42NjYgMS42NjYgMCAwIDEgMC0yLjQ2M0w3LjQ1OCAyMCA0LjY3IDE3LjQ1MyAxLjUwNyAxNC41N2ExLjY2NSAxLjY2NSAwIDAgMSAwLTIuNDYzbDIuMi0yYTEuNjY1IDEuNjY1IDAgMCAxIDIuMTMtLjA5N2w2Ljg2MyA1LjIwOUwyOC40NTIuODQ0YTIuNDg4IDIuNDg4IDAgMCAxIDEuODQxLS43MjljLjM1MS4wMDkuNjk5LjA5MSAxLjAxOS4yNDVsOC4yMzYgMy45NjFhMi41IDIuNSAwIDAgMSAxLjQxNSAyLjI1M3YuMDk5LS4wNDVWMzMuMzd2LS4wNDUuMDk1YTIuNTAxIDIuNTAxIDAgMCAxLTEuNDE2IDIuMjU3bC04LjIzNSAzLjk2MWEyLjQ5MiAyLjQ5MiAwIDAgMS0xLjA3Ny4yNDZabS43MTYtMjguOTQ3LTExLjk0OCA5LjA2MiAxMS45NTIgOS4wNjUtLjAwNC0xOC4xMjdaIi8+PC9zdmc+)](https://vscode.stainless.com/mcp/%7B%22name%22%3A%22graphor-mcp%22%2C%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22graphor-mcp%22%5D%2C%22env%22%3A%7B%22GRAPHOR_API_KEY%22%3A%22My%20API%20Key%22%7D%7D)

Or manually add it to your MCP client's configuration:

```json
{
  "mcpServers": {
    "graphor_api": {
      "command": "npx",
      "args": ["-y", "graphor-mcp@latest"],
      "env": {
        "GRAPHOR_API_KEY": "grlm_your_api_key_here"
      }
    }
  }
}
```

> Note: You can get your API key from [graphorlm.com](https://graphorlm.com).

## Remote MCP Server

For web-based AI clients (e.g. Claude.ai) or agentic frameworks (e.g. LangChain, CrewAI) that cannot run local `npx` processes, use the hosted remote MCP server. Authentication is handled via **OAuth** — a browser window will open for you to log in.

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
