# AI-RAN GraphRAG Backend Deployment

`git clone https://github.com/makbah/edgequake-airan.git`

`cd edgequake/docker`

## Environment Variables

Create a `.env` file:
`cp .env.example .env`
Update based on your LLM Provider

Option A: OpenAI

```bash
# Required
EDGEQUAKE_LLM_PROVIDER=openai
EDGEQUAKE_EMBEDDING_PROVIDER=openai
EDGEQUAKE_DEFAULT_LLM_PROVIDER=openai
EDGEQUAKE_DEFAULT_LLM_MODEL=gpt-4o-mini
EDGEQUAKE_DEFAULT_EMBEDDING_PROVIDER=openai
EDGEQUAKE_DEFAULT_EMBEDDING_MODEL=text-embedding-3-small
EDGEQUAKE_DEFAULT_EMBEDDING_DIMENSION=1536
EDGEQUAKE_VISION_PROVIDER=openai
EDGEQUAKE_VISION_MODEL=gpt-4.1-nano
OPENAI_API_KEY=sk-proj

# Optional
EDGEQUAKE_PORT=8080
POSTGRES_PASSWORD=edgequake_secret
```

Option B: Ollama

```bash
# Required
EDGEQUAKE_LLM_PROVIDER=ollama
EDGEQUAKE_EMBEDDING_PROVIDER=ollama
EDGEQUAKE_DEFAULT_LLM_PROVIDER=ollama
EDGEQUAKE_DEFAULT_LLM_MODEL=gemma3:12b
EDGEQUAKE_DEFAULT_EMBEDDING_PROVIDER=ollama
EDGEQUAKE_DEFAULT_EMBEDDING_MODEL=embeddinggemma
EDGEQUAKE_DEFAULT_EMBEDDING_DIMENSION=768
EDGEQUAKE_VISION_PROVIDER=ollama
EDGEQUAKE_VISION_MODEL=llava
OLLAMA_HOST=http://host.docker.internal:11434 # If ollama running on host machine

# Optional
EDGEQUAKE_PORT=8080
POSTGRES_PASSWORD=edgequake_secret
```

Option C: vLLM (self-hosted, OpenAI-compatible)

```bash
Required
EDGEQUAKE_LLM_PROVIDER=openai
EDGEQUAKE_EMBEDDING_PROVIDER=openai
EDGEQUAKE_DEFAULT_LLM_PROVIDER=openai
EDGEQUAKE_DEFAULT_LLM_MODEL=meta-llama/Llama-3-8b-instruct
EDGEQUAKE_DEFAULT_EMBEDDING_PROVIDER=openai
EDGEQUAKE_DEFAULT_EMBEDDING_MODEL=your-embedding-model
EDGEQUAKE_DEFAULT_EMBEDDING_DIMENSION=1536
EDGEQUAKE_VISION_PROVIDER=openai
EDGEQUAKE_VISION_MODEL=your-vision-model
OPENAI_API_KEY=placeholder          # vLLM accepts any non-empty string
OPENAI_BASE_URL=http://host.docker.internal:8000/v1 # vLLM port

# Optional
EDGEQUAKE_PORT=8080
POSTGRES_PASSWORD=edgequake_secret
```

`docker compose -f docker-compose.yml up --build` # TO-DO Package docker images into tar

Without Frontend: `docker compose -f docker-compose.yml up edgequake postgres`

## Services

| Service     | Port | Description              |
| ----------- | ---- | ------------------------ |
| `edgequake` | 8080 | EdgeQuake API server     |
| `postgres`  | 5432 | PostgreSQL with pgvector |

# AI-RAN GraphRAG MCP-Server Deployment

`cd ../../mcp` or from root `cd mcp`
`npm install`
`npm run build`

- Should have node_modules, dist/index.js (check ls -a) # TO-DO Package modules into tar

With Cursor (Offline): add to mcp.json

```
{
  "mcpServers": {
    "edgequake": {
      "command": "node",
      "args": ["absolute-path/edgequake-airan/mcp/dist/index.js"], # Make this the Actual Absolute Path to /dist/index.js
      "env": {
        "EDGEQUAKE_BASE_URL": "http://localhost:8080",
        "EDGEQUAKE_DEFAULT_TENANT": "00000000-0000-0000-0000-000000000002",
        "EDGEQUAKE_DEFAULT_WORKSPACE": "00000000-0000-0000-0000-000000000003"
      }
    }
  }
}
```

"EDGEQUAKE_DEFAULT_TENANT" : `curl -s http://localhost:8080/api/v1/tenants | python3 -m json.tool`

"EDGEQUAKE_DEFAULT_WORKSPACE" : curl -s http://localhost:8080/api/v1/tenants/<EDGEQUAKE_DEFAULT_TENANT>/workspaces | python3 -m json.tool
