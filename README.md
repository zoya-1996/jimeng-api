# Jimeng API

[中文文档](README.CN.md)

🎨 **Free AI Image and Video Generation API Service** - Based on reverse engineering of Jimeng AI (China site) and Dreamina (international site), providing an interface format compatible with the OpenAI API.

[![Node.js](https://img.shields.io/badge/Node.js-18+-green.svg)](https://nodejs.org/) [![TypeScript](https://img.shields.io/badge/TypeScript-5.0+-blue.svg)](https://www.typescriptlang.org/) [![Docker](https://img.shields.io/badge/Docker-Supported-blue.svg)](https://www.docker.com/) [![License](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](LICENSE)

## ✨ Features

- 🎨 **AI Image Generation**: Supports multiple models and resolutions (default 2K, supports 4K, 1K).
- 🖼️ **Image-to-Image Synthesis**: Supports local images or image URLs.
- 🎬 **AI Video Generation**: Supports text-to-video generation, and adds local image upload for image-to-video on the China site.
- 🌐 **International Site Support**: Added support for text-to-image and image-to-image APIs for the Dreamina international site. Please file an issue if you encounter problems.
- 💬 **Chat Interface**: OpenAI-compatible API for image generation.
- 🔄 **Smart Polling**: Adaptive polling mechanism to optimize generation efficiency.
- 🛡️ **Unified Exception Handling**: Comprehensive error handling and retry mechanism.
- 📊 **Detailed Logs**: Structured logging for easy debugging.
- 🐳 **Docker Support**: Containerized deployment, ready to use out of the box.
- ⚙️ **Log Level Control**: Dynamically adjust log output level through configuration files.

## ⚠ Risk Warning

- This project is for research and educational purposes only. It does not accept any financial donations or transactions!
- For personal use and research only. Avoid putting pressure on the official servers. Violators may have their accounts banned or, in serious cases, break the law!
- For personal use and research only. Avoid putting pressure on the official servers. Violators may have their accounts banned or, in serious cases, break the law!
- For personal use and research only. Avoid putting pressure on the official servers. Violators may have their accounts banned or, in serious cases, break the law!

## ✨ New Feature Highlights

### 📐 `ratio` and `resolution` Parameter Support

Image dimensions are now controlled by the `ratio` and `resolution` parameters, providing greater flexibility. The default `resolution` is set to `2k`.

```bash
curl -X POST http://localhost:5100/v1/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -d \
    "{\"model\": \"jimeng-4.0\", \"prompt\": \"A beautiful girl, film-like feel\", \"ratio\": \"4:3\", \"resolution\": \"2k\"}"
```

**Supported resolutions**: `1k`, `2k`, `4k`

**Supported ratios**: `1:1`, `4:3`, `3:4`, `16:9`, `9:16`, `3:2`, `2:3`, `21:9`

## 🚀 Quick Start

### Getting `sessionid`
- The method for obtaining the `sessionid` is the same for both the domestic site (Jimeng) and the international site (Dreamina), as shown in the image below.
> **Note 1**: The API endpoints are the same for both domestic and international sites, but for the international site's `sessionid`, you need to manually add the prefix **us-**, for example, `Bearer us-xxxxx`, to access the international site. Otherwise, it defaults to the domestic site.
> 
> **Note 2**: Both domestic and international sites now support *text-to-image* and *image-to-image*. The nanobanana model has been added for the international site.
> 
> **Note 3**: When using the nanobanana model on the international site, the generated images will be fixed at **1024x1024** and **2k**, consistent with the official settings.

![](https://github.com/iptag/jimeng-api/blob/main/get_sessionid.png)

### Environment Requirements

- Node.js 18+
- npm or yarn
- Docker (optional)

### Installation and Deployment

#### Method 1: Docker Image Pull and Update (Recommended)

**Pull command**
```bash
docker run -d \
  --name jimeng-api \
  -p 5100:5100 \
  --restart unless-stopped \
  ghcr.io/iptag/jimeng-api:latest
```

**Update command**
```bash
docker run --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower \
  --run-once jimeng-api
```

#### Method 2: Direct Run

```bash
# Clone the project
git clone <repository-url>
cd jimeng-api

# Install dependencies
npm install

# Build files
npm run build

# Start the service
npm run dev
```

#### Method 3: Docker Deployment

##### 🚀 Quick Start
```bash
# Using docker-compose (recommended)
docker-compose up -d

# Or build and run manually
docker build -t jimeng-api .

docker run -d \
  --name jimeng-api \
  -p 5100:5100 \
  --restart unless-stopped \
  jimeng-api
```

##### 🔧 Common Commands
```bash
# Rebuild and start
docker-compose up -d --build

# View service logs
docker logs jimeng-api

# Stop service
docker-compose down

# Enter container for debugging
docker exec -it jimeng-api sh
```

##### 📊 Docker Image Features
- ✅ **Multi-stage build**: Optimized image size (170MB)
- ✅ **Non-root user**: Enhanced security (jimeng user)
- ✅ **Health check**: Automatic service status monitoring
- ✅ **Unified port**: Uses port 5100 both inside and outside the container
- ✅ **Log management**: Structured log output

### Configuration Description

#### `configs/dev/service.yml`
```yaml
name: jimeng-api
route: src/api/routes/index.ts
port: 5100
```

#### `configs/dev/system.yml`
```yaml
requestLog: true
debug: false
log_level: info # Log levels: error, warning, info (default), debug
```

## 📖 API Documentation

### Text-to-Image

**POST** `/v1/images/generations`

**Request Parameters**:
- `model` (string): The name of the model to use.
- `prompt` (string): The text description of the image.
- `ratio` (string, optional): The aspect ratio of the image, defaults to `"1:1"`. Supported ratios: `1:1`, `4:3`, `3:4`, `16:9`, `9:16`, `3:2`, `2:3`, `21:9`.
- `resolution` (string, optional): The resolution level, defaults to `"2k"`. Supported resolutions: `1k`, `2k`, `4k`.
- `negative_prompt` (string, optional): Negative prompt words.
- `sample_strength` (number, optional): Sampling strength (0.0-1.0).
- `response_format` (string, optional): Response format ("url" or "b64_json").

```bash
# Default parameters (ratio: "1:1", resolution: "2k")
curl -X POST http://localhost:5100/v1/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -d \
    "{\"model\": \"jimeng-4.0\", \"prompt\": \"A cute little cat\"}"

# Example using 4K resolution
curl -X POST http://localhost:5100/v1/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -d \
    "{\"model\": \"jimeng-4.0\", \"prompt\": \"Magnificent landscape, ultra-high resolution\", \"ratio\": \"16:9\", \"resolution\": \"4k\"}"
```

**Supported Models**:
- `nanobanana`: Only supported on the international site.
- `jimeng-4.0`: Supported on both domestic and international sites.
- `jimeng-3.1`: Only supported on the domestic site.
- `jimeng-3.0`: Supported on both domestic and international sites.
- `jimeng-2.1`: Only supported on the domestic site.
- `jimeng-xl-pro`

**Supported Ratios and Corresponding Resolutions**:
| resolution | ratio | Resolution |
|---|---|---|
| `1k` | `1:1` | 1328×1328 |
| | `4:3` | 1472×1104 |
| | `3:4` | 1104×1472 |
| | `16:9` | 1664×936 |
| | `9:16` | 936×1664 |
| | `3:2` | 1584×1056 |
| | `2:3` | 1056×1584 |
| | `21:9` | 2016×864 |
| `2k` (default) | `1:1` | 2048×2048 |
| | `4:3` | 2304×1728 |
| | `3:4` | 1728×2304 |
| | `16:9` | 2560×1440 |
| | `9:16` | 1440×2560 |
| | `3:2` | 2496×1664 |
| | `2:3` | 1664×2496 |
| | `21:9` | 3024×1296 |
| `4k` | `1:1` | 4096×4096 |
| | `4:3` | 4608×3456 |
| | `3:4` | 3456×4608 |
| | `16:9` | 5120×2880 |
| | `9:16` | 2880×5120 |
| | `3:2` | 4992×3328 |
| | `2:3` | 3328×4992 |
| | `21:9` | 6048×2592 |

### Image-to-Image

**POST** `/v1/images/compositions`

**Function Description**: Generate a new image based on one or more input images, combined with a text prompt. Supports various creative modes like image blending, style transfer, and content synthesis.

```bash
# International version image-to-image example (local file upload)
# Note "us-your_international_token" below
curl -X POST http://localhost:5100/v1/images/compositions \
  -H "Authorization: Bearer us-YOUR_SESSION_ID" \
  -F "prompt=A cute cat, anime style" \
  -F "model=jimeng-4.0" \
  -F "images=@/path/to/your/local/cat.jpg"
```

**Request Parameters**:
- `model` (string): The name of the model to use.
- `prompt` (string): Text description of the image to guide the generation.
- `images` (array): An array of input images.
- `ratio` (string, optional): The aspect ratio of the image, defaults to `"1:1"`. Supported ratios: `1:1`, `4:3`, `3:4`, `16:9`, `9:16`, `3:2`, `2:3`, `21:9`.
- `resolution` (string, optional): The resolution level, defaults to `"2k"`. Supported resolutions: `1k`, `2k`, `4k`.
- `negative_prompt` (string, optional): Negative prompt words.
- `sample_strength` (number, optional): Sampling strength (0.0-1.0).
- `response_format` (string, optional): Response format ("url" or "b64_json").

**Usage Restrictions**:
- Number of input images: 1-10
- Supported image formats: Common formats like JPG, PNG, WebP, etc.
- Image size limit: Recommended not to exceed 10MB per image.
- Generation time: Usually 30 seconds to 5 minutes, complex compositions may take longer.

**Usage Examples**:

```bash
# Example 1: URL image style transfer (using application/json)
curl -X POST http://localhost:5100/v1/images/compositions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -d \
    "{\"model\": \"jimeng-4.0\", \"prompt\": \"Convert this photo into an oil painting style, with vibrant colors and distinct brushstrokes\", \"images\": [\"https://example.com/photo.jpg\"], \"ratio\": \"1:1\", \"resolution\": \"2k\", \"sample_strength\": 0.7}"

# Example 2: Local single file upload (using multipart/form-data)
curl -X POST http://localhost:5100/v1/images/compositions \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -F "prompt=A cute cat, anime style" \
  -F "model=jimeng-4.0" \
  -F "ratio=1:1" \
  -F "resolution=1k" \
  -F "images=@/path/to/your/local/cat.jpg"

# Example 3: Local multiple file upload (using multipart/form-data)
curl -X POST http://localhost:5100/v1/images/compositions \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -F "prompt=Merge these two images" \
  -F "model=jimeng-4.0" \
  -F "images=@/path/to/your/image1.jpg" \
  -F "images=@/path/to/your/image2.png"
```

**Successful Response Example** (applies to all examples above):
```json
{
  "created": 1703123456,
  "data": [
    {
      "url": "https://p3-sign.toutiaoimg.com/tos-cn-i-tb4s082cfz/abc123.webp"
    }
  ],
  "input_images": 1,
  "composition_type": "multi_image_synthesis"
}
```

#### ❓ **FAQ & Solutions**

**Q: What to do if image upload fails?**
A: Check if the image URL is accessible, ensure the image format is correct, and the file size does not exceed 10MB.

**Q: What to do if generation takes too long?**
A: Complex multi-image compositions take longer. Please be patient. If it's not completed after 10 minutes, you can resubmit the request.

**Q: How to improve composition quality?**
A:
- Use high-quality input images.
- Write detailed and accurate prompts.
- Adjust the `sample_strength` parameter appropriately.
- Avoid using too many conflicting image styles.

**Q: What image formats are supported?**
A: Common formats like JPG, PNG, WebP, GIF are supported. JPG or PNG are recommended.

**Q: Can I use local images?**
A: Yes. Direct upload of local files is now supported. Please refer to the "Local file upload example" above. You can also continue to use the network image URL method.

### Video Generation

**POST** `/v1/videos/generations`

**Function Description**: Generate a video based on a text prompt (Text-to-Video), or combined with input start/end frame images (Image-to-Video).

**Request Parameters**:
- `model` (string): The name of the video model to use.
- `prompt` (string): The text description of the video content.
- `width` (number, optional): Video width, defaults to `1024`.
- `height` (number, optional): Video height, defaults to `1024`.
- `resolution` (string, optional): Video resolution, e.g., `720p`.
- `file_paths` (array, optional): An array of image URLs to specify the **start frame** (1st element) and **end frame** (2nd element) of the video.
- `[file]` (file, optional): Local image files uploaded via `multipart/form-data` (up to 2) to specify the **start frame** and **end frame**. The field name can be arbitrary, e.g., `image1`.
- `response_format` (string, optional): Response format, supports `url` (default) or `b64_json`.

> **Image Input Description**:
> - You can provide input images via `file_paths` (URL array) or by directly uploading files.
> - If both methods are provided, the system will **prioritize the locally uploaded files**.
> - Up to 2 images are supported, the 1st as the start frame, the 2nd as the end frame.

**Supported Video Models**:
- `jimeng-video-3.0-pro` - Professional Edition
- `jimeng-video-3.0` - Standard Edition
- `jimeng-video-2.0-pro` - Professional Edition v2
- `jimeng-video-2.0` - Standard Edition v2

**Usage Examples**:

```bash
# Example 1: Pure text-to-video (using application/json)
curl -X POST http://localhost:5100/v1/videos/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -d \
    "{\"model\": \"jimeng-video-3.0\", \"prompt\": \"A lion running on the grassland\"}"

# Example 2: Upload local image as start frame for video generation (using multipart/form-data)
curl -X POST http://localhost:5100/v1/videos/generations \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -F "prompt=A man is talking" \
  -F "model=jimeng-video-3.0" \
  -F "image_file_1=@/path/to/your/local/image.png"

# Example 3: Use network image as start frame for video generation (using application/json)
curl -X POST http://localhost:5100/v1/videos/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -d \
    "{\"model\": \"jimeng-video-3.0\", \"prompt\": \"A woman dancing in a garden\", \"filePaths\": [\"https://example.com/your-network-image.jpg\"]}"

```

### Chat Completions

**POST** `/v1/chat/completions`

```bash
curl -X POST http://localhost:5100/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -d \
    "{\"model\": \"jimeng-4.0\", \"messages\": [ { \"role\": \"user\", \"content\": \"Draw a landscape painting\" } ]}"
```

## 🔍 API Response Format

### Image Generation Response
```json
{
  "created": 1759058768,
  "data": [
    {
      "url": "https://example.com/image1.jpg"
    },
    {
      "url": "https://example.com/image2.jpg"
    }
  ]
}
```

### Chat Completion Response
```json
{
  "id": "chatcmpl-123",
  "object": "chat.completion",
  "created": 1759058768,
  "model": "jimeng-4.0",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "![image](https://example.com/generated-image.jpg)"
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 10,
    "completion_tokens": 20,
    "total_tokens": 30
  }
}
```

### Stream Response (SSE)
```
data: {"id":"chatcmpl-123","object":"chat.completion.chunk","created":1759058768,"model":"jimeng-4.0","choices":[{"index":0,"delta":{"role":"assistant","content":"🎨 Generating image, please wait..."},"finish_reason":null}]}

data: {"id":"chatcmpl-123","object":"chat.completion.chunk","created":1759058768,"model":"jimeng-4.0","choices":[{"index":1,"delta":{"role":"assistant","content":"![image](https://example.com/image.jpg)"},"finish_reason":"stop"}]}

data: [DONE]
```

## 🏗️ Project Architecture

```
jimeng-api/
├── src/
│   ├── api/
│   │   ├── controllers/          # Controller layer
│   │   │   ├── core.ts          # Core functions (network requests, file handling)
│   │   │   ├── images.ts        # Image generation logic
│   │   │   ├── videos.ts        # Video generation logic
│   │   │   └── chat.ts          # Chat interface logic
│   │   ├── routes/              # Route definitions
│   │   └── consts/              # Constant definitions
│   ├── lib/                     # Core library
│   │   ├── configs/            # Configuration loading
│   │   ├── consts/             # Constants
│   │   ├── exceptions/         # Exception classes
│   │   ├── interfaces/         # Interface definitions
│   │   ├── request/            # Request handling
│   │   ├── response/           # Response handling
│   │   ├── config.ts           # Configuration center
│   │   ├── server.ts           # Server core
│   │   ├── logger.ts           # Logger
│   │   ├── error-handler.ts    # Unified error handling
│   │   ├── smart-poller.ts     # Smart poller
│   │   └── aws-signature.ts    # AWS signature
│   ├── daemon.ts               # Daemon process
│   └── index.ts               # Entry file
├── configs/                    # Configuration files
├── Dockerfile                 # Docker configuration
└── package.json              # Project configuration
```

## 🔧 Core Components

### SmartPoller
- Adapts polling interval based on status codes.
- Multiple exit conditions to avoid invalid waiting.
- Detailed progress tracking and logging.

### Unified ErrorHandler
- Categorized error handling (network errors, API errors, timeouts, etc.).
- Automatic retry mechanism.
- User-friendly error messages.

### Safe JSON Parsing
- Automatically fixes common JSON format issues.
- Supports trailing commas and single quotes.
- Detailed parsing error logs.

## ⚙️ Advanced Configuration

### Polling Configuration
```typescript
export const POLLING_CONFIG = {
  MAX_POLL_COUNT: 900,    // Max polling attempts (15 minutes)
  POLL_INTERVAL: 1000,    // Base polling interval (1 second)
  STABLE_ROUNDS: 5,       // Stable rounds
  TIMEOUT_SECONDS: 900    // Timeout (15 minutes)
};
```

### Retry Configuration
```typescript
export const RETRY_CONFIG = {
  MAX_RETRY_COUNT: 3,     // Max retry attempts
  RETRY_DELAY: 5000       // Retry delay (5 seconds)
};
```

## 🐛 Troubleshooting

### Common Issues

1.  **JSON Parsing Error**
    -   Check if the request body format is correct.
    -   The system will automatically fix common format issues.

2.  **Invalid `sessionid`**
    -   Re-obtain the `sessionid` for the corresponding site.
    -   Check if the `sessionid` format is correct.

3.  **Generation Timeout**
    -   Image generation: usually 1-3 minutes.
    -   Video generation: usually 3-15 minutes.
    -   The system will automatically handle timeouts.

4.  **Insufficient Credits**
    -   Go to the Jimeng/Dreamina official website to check your credit balance.
    -   The system will provide detailed credit status information.

## 🙏 Acknowledgements

This project is based on the contributions and inspiration of the following open-source project:

- **[jimeng-free-api-all](https://github.com/wwwzhouhui/jimeng-free-api-all)** - Thanks to this project for providing an important reference and technical basis for the reverse engineering of the Jimeng API. This project has improved its functionality and architecture based on it.

## 📄 License

GPL v3 License - see the [LICENSE](LICENSE) file for details.

## ⚠️ Disclaimer

This project is for learning and research purposes only. Please comply with relevant service terms and laws. Any consequences arising from the use of this project are the sole responsibility of the user.