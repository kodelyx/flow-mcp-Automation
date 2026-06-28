# Flow MCP Deployment & Connection Guide

This directory contains everything you need to run and connect the Google Flow MCP server locally or remotely on any PC.

---

## 📂 Files inside this directory
1. `docker-compose.yml` — Runs the Flow API & MCP server container.
2. `local_mcp.json` — Configuration code for Cursor / Desktop apps (Local Stdio mode).
3. `public.mcp` — General reference cheat-sheet for ChatGPT.

---

## 🚀 1. Deploying the Server (On Any PC)

To run the server on any machine:
1. Ensure **Docker** is installed and running.
2. Open terminal in this folder and run:
   ```bash
   docker-compose up -d
   ```
3. Load Chrome and verify that your **Flow Chrome Extension** is connected.

*Note: If you use a different Cloudflare subdomain on another PC, edit the `PUBLIC_BASE_URL` value in `docker-compose.yml` to match your new subdomain before starting.*

---

## 🔌 2. Connecting to Cursor / Desktop Apps

### A. Local Connection (Same PC where Docker is running)
1. Open Cursor Settings (`Cmd + ,`) -> **Models** -> **MCP**.
2. Click **+ Add New MCP Server**.
3. Configure:
   * **Name**: `flow`
   * **Type**: `command`
   * **Command**: `docker exec -i flow-agent-server python3 -u /app/flow_mcp_server.py`
   *(This configuration is also saved in `local_mcp.json` for reference).*

### B. Remote Connection (Different PC or Different Network)
If your container is running on your home PC, but you are coding on a laptop elsewhere:
1. Open Cursor Settings -> **Models** -> **MCP**.
2. Click **+ Add New MCP Server**.
3. Configure:
   * **Name**: `flow-remote`
   * **Type**: `SSE`
   * **URL**: `https://flow.chatbulky.com/sse` (Replace with your tunnel subdomain if different)

---

## 🌐 3. Connecting to ChatGPT Web (Chrome)

For web-based ChatGPT:
1. Add a Custom Connector / Custom MCP tool in your browser extension or settings.
2. Select **SSE** connection type.
3. Paste the URL:
   ```text
   https://flow.chatbulky.com/sse
   ```
4. Set Authentication to **None** (No OAuth).
