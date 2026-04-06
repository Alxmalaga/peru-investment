---
applyTo: "**/mcp-server/**, **/mcp/**, **/*mcp*.ts, **/*mcp*.py, **/server.ts, **/server.py"
---

# MCP Builder Skill — Model Context Protocol Server Development

Activate when: user wants to build an MCP server, integrate external APIs via MCP, or create tools for LLMs using the MCP protocol.

## Recommended Stack

- **Language**: TypeScript (preferred) — strong SDK support, static typing, broad model familiarity
- **Transport**: Streamable HTTP for remote servers (stateless JSON); stdio for local servers
- **Validation**: Zod (TypeScript) or Pydantic (Python)

## 4-Phase Development Process

### Phase 1: Research & Planning

1. Study the MCP spec: start at `https://modelcontextprotocol.io/sitemap.xml`
2. Load SDK docs:
   - TypeScript: `https://raw.githubusercontent.com/modelcontextprotocol/typescript-sdk/main/README.md`
   - Python: `https://raw.githubusercontent.com/modelcontextprotocol/python-sdk/main/README.md`
3. Review the target API documentation (endpoints, auth, data models)
4. Plan tools: prioritize comprehensive API coverage over workflow-specific shortcuts

### Phase 2: Implementation

#### Tool Design Principles

- **Naming**: Use consistent prefixes + action verbs — `github_create_issue`, `github_list_repos`
- **Descriptions**: Concise but complete — what it does, parameters, return type
- **Input schemas**: Use Zod / Pydantic with constraints and examples in field descriptions
- **Output**: Return structured data + human-readable text when possible
- **Errors**: Actionable messages — guide the agent toward the solution

#### Annotations (always include)

```typescript
annotations: {
  readOnlyHint: true,      // Does not modify state
  destructiveHint: false,  // Cannot be undone
  idempotentHint: true,    // Safe to retry
  openWorldHint: false,    // Works on fixed known data
}
```

#### TypeScript Pattern

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { z } from "zod";

const server = new McpServer({ name: "my-server", version: "1.0.0" });

server.registerTool("resource_list", {
  description: "List all resources with optional filter",
  inputSchema: {
    filter: z.string().optional().describe("Filter by name prefix"),
    limit: z.number().min(1).max(100).default(20),
  },
  annotations: { readOnlyHint: true },
}, async ({ filter, limit }) => {
  try {
    const results = await apiClient.list({ filter, limit });
    return {
      content: [{ type: "text", text: JSON.stringify(results, null, 2) }],
      structuredContent: results,
    };
  } catch (error) {
    throw new Error(`Failed to list resources: ${error.message}. Check your API key.`);
  }
});
```

#### Python Pattern (FastMCP)

```python
from mcp.server.fastmcp import FastMCP
from pydantic import BaseModel

mcp = FastMCP("my-server")

class ListParams(BaseModel):
    filter: str | None = None
    limit: int = 20

@mcp.tool
async def resource_list(params: ListParams) -> dict:
    """List all resources with optional filter."""
    results = await api_client.list(filter=params.filter, limit=params.limit)
    return results
```

### Phase 3: Build & Test

```bash
# TypeScript
npm run build
npx @modelcontextprotocol/inspector  # Visual testing UI

# Python
python -m py_compile your_server.py
npx @modelcontextprotocol/inspector
```

#### Code Quality Checklist

- [ ] No duplicated code (DRY)
- [ ] Consistent error handling with actionable messages
- [ ] Full type coverage (TypeScript) or type hints (Python)
- [ ] All tools have clear descriptions
- [ ] Pagination support for list operations
- [ ] Auth credentials via environment variables — never hardcoded

### Phase 4: Evaluations

Create 10 test questions as an XML evaluation file:

```xml
<evaluation>
  <qa_pair>
    <question>Find the most recently created issue in the 'backend' repo tagged 'bug'. What is its title?</question>
    <answer>Database connection timeout on high load</answer>
  </qa_pair>
</evaluation>
```

Each question must be:
- **Independent** — not dependent on other questions
- **Read-only** — only non-destructive tool calls required
- **Complex** — requires multiple tool calls
- **Verifiable** — single clear answer
- **Stable** — answer won't change over time

## Project Structure (TypeScript)

```
my-mcp-server/
├── src/
│   ├── server.ts       # MCP server setup
│   ├── tools/          # One file per tool group
│   │   ├── issues.ts
│   │   └── repos.ts
│   ├── client.ts       # API client + auth
│   └── utils.ts        # Shared helpers
├── package.json
├── tsconfig.json
└── eval.xml
```

## Claude Desktop Integration (`claude_desktop_config.json`)

```json
{
  "mcpServers": {
    "my-server": {
      "command": "node",
      "args": ["/path/to/build/server.js"],
      "env": { "API_KEY": "your-key-here" }
    }
  }
}
```

## Common Pitfalls

- ❌ Hardcoding API keys — always use `process.env` / `os.environ`
- ❌ Returning too much data — filter and paginate responses
- ❌ Vague error messages — always include next steps
- ❌ Missing `readOnlyHint` — agents use this to decide when to confirm actions
- ❌ Skipping the Inspector test — always verify tools work before shipping
