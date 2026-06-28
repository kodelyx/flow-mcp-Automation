# Flow MCP: Native Google Flow AI Integration

This repository contains the configuration and guides to run and connect the Google Flow MCP server locally or remotely.

---

## 📂 Files in this Repository
* **`docker-compose.yml`** — Configuration to run the Flow API & MCP server container.

---

## 🚀 1. Deploying the Server (On Any PC)

To start the server:
1. Ensure **Docker** is running.
2. Run this command in the repository folder:
   ```bash
   docker-compose up -d
   ```
3. Load Chrome and verify that your **Flow Chrome Extension** is connected.

*Note: If you deploy on another PC with a different Cloudflare subdomain, update the `PUBLIC_BASE_URL` in `docker-compose.yml` before starting.*

---

## 🔌 2. Connecting to Cursor / Desktop Apps

### A. Local Connection (Same PC as Docker)
Open Cursor Settings (`Cmd + ,`) -> **Models** -> **MCP** -> **Add New MCP Server**:
* **Name**: `flow`
* **Type**: `command`
* **Command**: `docker exec -i flow-agent-server python3 -u /app/flow_mcp_server.py`

*For Claude Desktop / ChatGPT Desktop (Codex), paste this into your configuration JSON:*
```json
{
  "mcpServers": {
    "flow": {
      "command": "docker",
      "args": [
        "exec",
        "-i",
        "flow-agent-server",
        "python3",
        "-u",
        "/app/flow_mcp_server.py"
      ]
    }
  }
}
```

### B. Remote Connection (Different PC or Network)
To connect to your server from another machine over the internet:
Open Cursor Settings -> **Models** -> **MCP** -> **Add New MCP Server**:
* **Name**: `flow-remote`
* **Type**: `SSE`
* **URL**: `https://flow.chatbulky.com/sse`

---

## 🌐 3. Connecting to ChatGPT Web (Chrome)

For browser-based ChatGPT (Web):
1. Add a Custom Connector/MCP tool in your ChatGPT Web interface.
2. Select **SSE** transport type.
3. Use the URL:
   ```text
   https://flow.chatbulky.com/sse
   ```
4. Set Authentication to **None** (Disable OAuth).
