# TypeScript MCP Server Guide

## Project Structure

```
my-mcp-server/
├── src/
│   ├── index.ts          # Server setup + tool registration
│   ├── tools/            # One file per tool
│   └── resources/        # One file per resource
├── package.json
└── tsconfig.json
```

## Tool with Validation

```typescript
server.tool(
  'search-files',
  {
    query: { type: 'string', description: 'Search query' },
    path: { type: 'string', description: 'Directory to search', default: '.' },
    maxResults: { type: 'number', description: 'Maximum results', default: 10 },
  },
  async ({ query, path, maxResults }) => {
    if (!query.trim()) {
      return { content: [{ type: 'text', text: 'Error: query cannot be empty' }], isError: true };
    }
    const results = await searchFiles(query, path, maxResults);
    return { content: [{ type: 'text', text: JSON.stringify(results, null, 2) }] };
  }
);
```

## Testing

```bash
echo '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"greet","arguments":{"name":"World"}}}' | node dist/index.js
```
