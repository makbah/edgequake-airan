git clone https://github.com/makbah/edgequake-airan.git

cd edgequake/docker
cp .env.example .env
docker compose -f docker-compose.yml up --build

cd ../../mcp
npm install
npm run build

- Should have node_modules, dist/index.js

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

"EDGEQUAKE_DEFAULT_TENANT" : curl -s http://localhost:8080/api/v1/tenants | python3 -m json.tool
"EDGEQUAKE_DEFAULT_WORKSPACE" : curl -s http://localhost:8080/api/v1/tenants/<EDGEQUAKE_DEFAULT_TENANT>/workspaces | python3 -m json.tool
