# Python MCP Server Guide

## Project Structure

```
my-mcp-server/
├── src/
│   ├── __init__.py
│   ├── server.py         # Server setup + tool registration
│   ├── tools/
│   └── resources/
├── pyproject.toml
└── requirements.txt
```

## Tool with Validation

```python
from mcp.server import Server
from mcp.types import TextContent

server = Server("my-server")

@server.tool("search-files")
async def search_files(query: str, path: str = ".", max_results: int = 10) -> list[TextContent]:
    """Search for files matching a query."""
    if not query.strip():
        raise ValueError("Query cannot be empty")
    results = await do_search(query, path, max_results)
    return [TextContent(type="text", text=json.dumps(results, indent=2))]
```

## Configuration

```json
{
  "mcpServers": {
    "my-server": {
      "command": "python",
      "args": ["-m", "src.server"]
    }
  }
}
```
