# ⚡ Flow Automation and MCP Setup

This guide documents the OpenAI-compatible REST API and MCP endpoints exposed by the standalone **Flow Automation Backend Server** running on `http://localhost:8001`.

---

## 🟢 1. Server Health Check

Check if the server is healthy, and if the host Chrome Extension is connected and authorized with a valid Google Flow token.

* **Endpoint**: `GET /health`
* **cURL Request**:
  ```bash
  curl -X GET http://localhost:8001/health
  ```
* **Success Response (JSON)**:
  ```json
  {
    "status": "healthy",
    "extension_connected": true,
    "has_flow_key": true
  }
  ```

---

## 🎨 2. Image Generation (Text-to-Image / Image-to-Image)

Generate images from a text prompt. Optional start/reference images can be passed using base64.

* **Endpoint**: `POST /v1/images/generations`
* **Request Payload Parameters**:
  * `prompt` (string, Required): Text description of the image.
  * `model` (string, Optional): The model to use (default: `"narwhal"`).
  * `n` (integer, Optional): Number of images to generate (default: `1`).
  * `size` (string, Optional): Image dimensions (default: `"1280x720"`).
  * `image_base64` (string, Optional): Base64 encoded image string (e.g. `data:image/png;base64,...`) for Image-to-Image composition.

* **cURL Request (Text-to-Image)**:
  ```bash
  curl -X POST http://localhost:8001/v1/images/generations \
       -H "Content-Type: application/json" \
       -d '{
         "prompt": "A cute 3D Pixar-style golden-brown potato boy smiling happily.",
         "size": "1280x720",
         "n": 1
       }'
  ```

* **Success Response (JSON)**:
  ```json
  {
    "created": 1782659724,
    "data": [
      {
        "url": "http://localhost:8001/download/flowagent_img_1782659724_44881a_1.png",
        "media_id": "flowagent_img_1782659724_44881a_1.png"
      }
    ]
  }
  ```

---

## 🎬 3. Video Generation (Text-to-Video / Image-to-Video)

Generate a cinematic video clip. Can start from a reference static image to ensure visual continuity (Image-to-Video).

* **Endpoint**: `POST /v1/videos/generations`
* **Request Payload Parameters**:
  * `prompt` (string, Required): Description of the video motion.
  * `aspect` (string, Optional): Aspect ratio (`"landscape"` or `"portrait"`, default: `"portrait"`).
  * `duration` (integer, Optional): Video length in seconds (default: `10`).
  * `image_base64` (string, Optional): Base64 encoded image string (e.g. `data:image/png;base64,...`) to use as the starting frame.

* **cURL Request (Image-to-Video)**:
  ```bash
  curl -X POST http://localhost:8001/v1/videos/generations \
       -H "Content-Type: application/json" \
       -d '{
         "prompt": "A cute 3D Pixar-style golden-brown potato boy waving happily.",
         "aspect": "landscape",
         "duration": 10,
         "image_base64": "data:image/png;base64,iVBORw0KGgoAAA..."
       }'
  ```

* **Success Response (JSON)**:
  ```json
  {
    "data": [
      {
        "url": "http://localhost:8001/download/flow_vid_1782660048_47bedd_1.mp4"
      }
    ]
  }
  ```

---

## 🪙 4. Get Google Flow Credits

Check remaining credits/generations on the logged-in Google Flow account.

* **Endpoint**: `GET /v1/credits`
* **cURL Request**:
  ```bash
  curl -X GET http://localhost:8001/v1/credits
  ```
* **Success Response (JSON)**:
  ```json
  {
    "credits": 240
  }
  ```

---

## 📂 5. Download Generated Media Files

Directly download files from the container's generated output directory.

* **Endpoint**: `GET /download/{filename}`
* **cURL Request**:
  ```bash
  curl -o output.mp4 http://localhost:8001/download/flow_vid_1782660048_47bedd_1.mp4
  ```

---

## 🐍 6. Python Usage Example

Here is a full Python example using the `requests` library to trigger an Image-to-Video generation:

```python
import base64
import requests

# 1. Encode starting frame image to base64
image_path = "test_potato_boy.png"
with open(image_path, "rb") as f:
    img_b64 = f"data:image/png;base64,{base64.b64encode(f.read()).decode('utf-8')}"

# 2. Prepare payload
payload = {
    "prompt": "A cute 3D Pixar-style golden-brown potato boy waving happily in a vegetable market.",
    "aspect": "landscape",
    "duration": 10,
    "image_base64": img_b64
}

# 3. Call API
url = "http://localhost:8001/v1/videos/generations"
response = requests.post(url, json=payload)
data = response.json()

# 4. Download output video
if "data" in data and len(data["data"]) > 0:
    video_url = data["data"][0]["url"]
    video_content = requests.get(video_url).content
    with open("output.mp4", "wb") as out_file:
        out_file.write(video_content)
    print("🎉 Video successfully saved to output.mp4")
```

---

## 🛠️ 7. Model Context Protocol (MCP) Setup

You can integrate this Flow Automation service directly into Cursor or Claude Desktop to use it as a native AI tool.

### Cursor Setup:
1. Open Cursor Settings (`Cmd + ,`) -> **Models** -> **MCP**.
2. Click **+ Add New MCP Server**.
3. Configure the following:
   * **Name**: `flow-agent`
   * **Type**: `command`
   * **Command**: `docker exec -i flow-agent-server python3 -u /app/flow_mcp_server.py`

### Claude Desktop Setup:
Add the configuration to `~/Library/Application Support/Claude/claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "flow-agent": {
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
