---
title: "Anthropic Model Context Protocol (MCP)"
date: 2025-02-28
---

# Elasticsearch

1. Install Python
1. Install `uv`: `pip install uv`
1. Clone https://github.com/cr7258/elasticsearch-mcp-server
1. Setup Claude

## uvx Setup
```json
{
    "mcpServers": {
        "elasticsearch": {
            "command": "uvx",
            "args": [
                "elasticsearch-mcp-server"
            ],
            "env": {
                "ELASTIC_HOST": "http://localhost:9200",
                "ELASTIC_USERNAME": "elastic",
                "ELASTIC_PASSWORD": "changeme"
            }
        }
    }
}
```

## uv Setup
```json
{
    "mcpServers": {
      "elasticsearch": {
        "command": "uv",
        "args": [
          "--directory",
          "C:\\Repositories\\mcp\\elasticsearch-mcp-server",
          "run",
          "elasticsearch-mcp-server"
        ],
        "env": {
          "ELASTIC_HOST": "http://localhost:9200",
          "ELASTIC_USERNAME": "elastic",
          "ELASTIC_PASSWORD": "changeme"
        }
      }
    }
  }
```

# File System

## Setup
```json
{
    "mcpServers": {        
        "filesystem": {
            "command": "npx",
            "args": [
                "-y",
                "@modelcontextprotocol/server-filesystem",
                "C:\\Users\\dani\\Desktop"
            ]
        }
    }
}
```

# Atlassian