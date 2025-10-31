# Jimeng API

🎨 **免费的AI图像和视频生成API服务** - 基于即梦AI（国内站）和dreamina（国际站）的逆向工程实现，提供与OpenAI API兼容的接口格式

[![Node.js](https://img.shields.io/badge/Node.js-18+-green.svg)](https://nodejs.org/) [![TypeScript](https://img.shields.io/badge/TypeScript-5.0+-blue.svg)](https://www.typescriptlang.org/) [![Docker](https://img.shields.io/badge/Docker-支持-blue.svg)](https://www.docker.com/) [![License](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](LICENSE)

## ✨ 特性

- 🎨 **AI图像生成**: 支持多种模型和分辨率（默认2K，支持4K，1K）
- 🖼️ **图生图合成**: 支持本地图片或者图片URL
- 🎬 **AI视频生成**: 支持文本到视频生成，增加国内站图生视频的本地图片上传功能
- 🌐 **国际站支持**: 新增对即梦国际站（dreamina）文生图以及图生图API的支持，有问题提issue
- 💬 **聊天接口**: OpenAI生图格式兼容的API
- 🔄 **智能轮询**: 自适应轮询机制，优化生成效率
- 🛡️ **统一异常处理**: 完善的错误处理和重试机制
- 📊 **详细日志**: 结构化日志记录，便于调试
- 🐳 **Docker支持**: 容器化部署，开箱即用
- ⚙️ **日志级别控制**: 可通过配置文件动态调整日志输出级别

## ⚠ 风险警告

- 此项目属于研究交流学习性质，不接受任何资金捐助和金钱交易！
- 仅限自用和个人研究，避免对官方造成服务压力，否则轻者可能封号，重者可能触犯法律！
- 仅限自用和个人研究，避免对官方造成服务压力，否则轻者可能封号，重者可能触犯法律！
- 仅限自用和个人研究，避免对官方造成服务压力，否则轻者可能封号，重者可能触犯法律！

## ✨ 新功能亮点

### 📐 ratio和resolution参数支持

现在通过`ratio`和`resolution`两个参数来共同控制图像尺寸，这提供了更高的灵活性。程序内`resolution`默认设置为`2k`。

```bash
curl -X POST http://localhost:5100/v1/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -d '{
    "model": "jimeng-4.0",
    "prompt": "美丽的少女，胶片感",
    "ratio": "4:3",
    "resolution": "2k"
  }'
```

**支持的resolution**: `1k`, `2k`, `4k`

**支持的ratio**: `1:1`, `4:3`, `3:4`, `16:9`, `9:16`, `3:2`, `2:3`, `21:9`

## 🚀 快速开始

### sessionid获取
- 国内站 (即梦)和国际站 (dreamina)获取sessionid的方法相同，见下图。
> **注意1**: 国内站和国际站api接口相同，但国际站的sessionid需要手动添加**us-**，比如`Bearer us-xxxxx`，才能访问国际站，否则默认国内站。
>
> **注意2**: 国内站和国际站现已同时支持*文生图*和*图生图*，国际站添加nanobanana模型。
>
> **注意3**: 国际站使用nanobanana模型时，生成的图像都将固定为 **1024x1024** 和 **2k**，与官方保持一致。

![](https://github.com/iptag/jimeng-api/blob/main/get_sessionid.png)

### 环境要求

- Node.js 18+
- npm 或 yarn
- Docker (可选)

### 安装部署

#### 方式一：docker镜像拉取和更新

**拉取命令**
```bash
docker run -d \
  --name jimeng-api \
  -p 5100:5100 \
  --restart unless-stopped \
  ghcr.io/iptag/jimeng-api:latest
```

**更新命令**
```bash
docker run --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower \
  --run-once jimeng-api
```

#### 方式二：直接运行

```bash
# 克隆项目
git clone <repository-url>
cd jimeng-api

# 安装依赖
npm install

# 编译文件
npm run build

# 启动服务
npm run dev
```

#### 方式三：Docker部署（推荐）

##### 🚀 快速启动
```bash
# 使用docker-compose（推荐）
docker-compose up -d

# 或者手动构建和运行
docker build -t jimeng-api .

docker run -d \
  --name jimeng-api \
  -p 5100:5100 \
  --restart unless-stopped \
  jimeng-api
```

##### 🔧 常用命令
```bash
# 重新构建并启动
docker-compose up -d --build

# 查看服务日志
docker logs jimeng-api

# 停止服务
docker-compose down

# 进入容器调试
docker exec -it jimeng-api sh
```

##### 📊 Docker镜像特性
- ✅ **多阶段构建**：优化镜像大小（167MB）
- ✅ **非root用户**：增强安全性（jimeng用户）
- ✅ **健康检查**：自动监控服务状态
- ✅ **统一端口**：容器内外均使用5100端口
- ✅ **日志管理**：结构化日志输出

### 配置说明

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
log_level: info # 日志级别: error, warning, info(默认), debug
```

## 📖 API文档

### 文生图

**POST** `/v1/images/generations`

**请求参数**:
- `model` (string): 使用的模型名称
- `prompt` (string): 图像描述文本
- `ratio` (string, 可选): 图像比例，默认为 `"1:1"`。支持的比例: `1:1`, `4:3`, `3:4`, `16:9`, `9:16`, `3:2`, `2:3`, `21:9`。
- `resolution` (string, 可选): 分辨率级别，默认为 `"2k"`。支持的分辨率: `1k`, `2k`, `4k`。
- `negative_prompt` (string, 可选): 负面提示词
- `sample_strength` (number, 可选): 采样强度 (0.0-1.0)
- `response_format` (string, 可选): 响应格式 ("url" 或 "b64_json")

```bash
# 默认参数（ratio: "1:1", resolution: "2k"）
curl -X POST http://localhost:5100/v1/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -d '{
    "model": "jimeng-4.0",
    "prompt": "一只可爱的小猫咪"
  }'

# 使用4K分辨率的示例
curl -X POST http://localhost:5100/v1/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -d '{
    "model": "jimeng-4.0",
    "prompt": "壮丽的山水风景，超高分辨率",
    "ratio": "16:9",
    "resolution": "4k"
  }'
```

**支持的模型**:
- `nanobanana`: 仅国际站支持
- `jimeng-4.0`: 国内、国际站均支持
- `jimeng-3.1`: 仅国内站支持
- `jimeng-3.0`: 国内、国际站均支持
- `jimeng-2.1`: 仅国内站支持
- `jimeng-xl-pro`


**支持的比例及对应分辨率** ：
| resolution | ratio | 分辨率 |
|---|---|---|
| `1k` | `1:1` | 1328×1328 |
| | `4:3` | 1472×1104 |
| | `3:4` | 1104×1472 |
| | `16:9` | 1664×936 |
| | `9:16` | 936×1664 |
| | `3:2` | 1584×1056 |
| | `2:3` | 1056×1584 |
| | `21:9` | 2016×864 |
| `2k` (默认) | `1:1` | 2048×2048 |
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

### 图生图

**POST** `/v1/images/compositions`

**功能说明**: 基于输入的一张或多张图片，结合文本提示词生成新的图片。支持图片混合、风格转换、内容合成等多种创作模式。

```bash
# 国际版图生图示例 (本地文件上传)
# 注意下面的 "us-your_international_token"
curl -X POST http://localhost:5100/v1/images/compositions \
  -H "Authorization: Bearer us-YOUR_SESSION_ID" \
  -F "prompt=A cute cat, anime style" \
  -F "model=jimeng-4.0" \
  -F "images=@/path/to/your/local/cat.jpg"
```

**请求参数**:
- `model` (string): 使用的模型名称
- `prompt` (string): 图像描述文本，用于指导生成方向
- `images` (array): 输入图片数组
- `ratio` (string, 可选): 图像比例，默认为 `"1:1"`。支持的比例: `1:1`, `4:3`, `3:4`, `16:9`, `9:16`, `3:2`, `2:3`, `21:9`。
- `resolution` (string, 可选): 分辨率级别，默认为 `"2k"`。支持的分辨率: `1k`, `2k`, `4k`。
- `negative_prompt` (string, 可选): 负面提示词
- `sample_strength` (number, 可选): 采样强度 (0.0-1.0)
- `response_format` (string, 可选): 响应格式 ("url" 或 "b64_json")

**使用限制**:
- 输入图片数量: 1-10张
- 支持的图片格式: JPG, PNG, WebP等常见格式
- 图片大小限制: 建议单张图片不超过10MB
- 生成时间: 通常30秒-5分钟，复杂合成可能需要更长时间

**使用示例**:

```bash
# 示例1: URL图片风格转换 (使用application/json)
curl -X POST http://localhost:5100/v1/images/compositions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -d '{
    "model": "jimeng-4.0",
    "prompt": "将这张照片转换为油画风格，色彩鲜艳，笔触明显",
    "images": ["https://example.com/photo.jpg"],
    "ratio": "1:1",
    "resolution": "2k",
    "sample_strength": 0.7
  }'

# 示例2: 本地单文件上传 (使用multipart/form-data)
curl -X POST http://localhost:5100/v1/images/compositions \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -F "prompt=一只可爱的猫，动漫风格" \
  -F "model=jimeng-4.0" \
  -F "ratio=1:1" \
  -F "resolution=1k" \
  -F "images=@/path/to/your/local/cat.jpg"

# 示例3: 本地多文件上传 (使用multipart/form-data)
curl -X POST http://localhost:5100/v1/images/compositions \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -F "prompt=融合这两张图片" \
  -F "model=jimeng-4.0" \
  -F "images=@/path/to/your/image1.jpg" \
  -F "images=@/path/to/your/image2.png"
```

**成功响应示例** (适用于以上所有示例):
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

#### ❓ **常见问题与解决方案**

**Q: 图片上传失败怎么办？**
A: 检查图片URL是否可访问，确保图片格式正确，文件大小不超过10MB。

**Q: 生成时间过长怎么办？**
A: 复杂的多图合成需要更长时间，建议耐心等待。如果超过10分钟仍未完成，可以重新提交请求。

**Q: 如何提高合成质量？**
A:
- 使用高质量的输入图片
- 编写详细准确的提示词
- 适当调整 `sample_strength` 参数
- 避免使用过多冲突的图片风格

**Q: 支持哪些图片格式？**
A: 支持 JPG、PNG、WebP、GIF 等常见格式，推荐使用 JPG 或 PNG。

**Q: 可以使用本地图片吗？**
A: 可以。现在支持直接上传本地文件。请参考上方的“本地文件上传示例”。您也可以继续使用原有的网络图片URL方式。

### 视频生成

**POST** `/v1/videos/generations`

**功能说明**: 基于文本提示词（Text-to-Video），或结合输入的首/尾帧图片（Image-to-Video）生成一段视频。

**请求参数**:
- `model` (string): 使用的视频模型名称。
- `prompt` (string): 视频内容的文本描述。
- `width` (number, 可选): 视频宽度，默认为 `1024`。
- `height` (number, 可选): 视频高度，默认为 `1024`。
- `resolution` (string, 可选): 视频分辨率，例如 `720p`。
- `file_paths` (array, 可选): 一个包含图片URL的数组，用于指定视频的**首帧**（数组第1个元素）和**尾帧**（数组第2个元素）。
- `[file]` (file, 可选): 通过 `multipart/form-data` 方式上传的本地图片文件（最多2个），用于指定视频的**首帧**和**尾帧**。字段名可以任意，例如 `image1`。
- `response_format` (string, 可选): 响应格式，支持 `url` (默认) 或 `b64_json`。

> **图片输入说明**:
> - 您可以通过 `file_paths` (URL数组) 或直接上传文件两种方式提供输入图片。
> - 如果两种方式同时提供，系统将**优先使用本地上传的文件**。
> - 最多支持2张图片，第1张作为视频首帧，第2张作为视频尾帧。

**支持的视频模型**:
- `jimeng-video-3.0-pro` - 专业版
- `jimeng-video-3.0` - 标准版
- `jimeng-video-2.0-pro` - 专业版v2
- `jimeng-video-2.0` - 标准版v2

**使用示例**:

```bash
# 示例1: 纯文本生成视频 (使用 application/json)
curl -X POST http://localhost:5100/v1/videos/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -d '{
    "model": "jimeng-video-3.0",
    "prompt": "一只奔跑在草原上的狮子"
  }'

# 示例2: 上传本地图片作为首帧生成视频 (使用 multipart/form-data)
curl -X POST http://localhost:5100/v1/videos/generations \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -F "prompt=一个男人在说话" \
  -F "model=jimeng-video-3.0" \
  -F "image_file_1=@/path/to/your/local/image.png"

# 示例3: 使用网络图片作为首帧生成视频 (使用 application/json)
curl -X POST http://localhost:5100/v1/videos/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -d '{
    "model": "jimeng-video-3.0",
    "prompt": "一个女人在花园里跳舞",
    "filePaths": ["https://example.com/your-network-image.jpg"]
  }'

```

### 聊天完成

**POST** `/v1/chat/completions`

```bash
curl -X POST http://localhost:5100/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_SESSION_ID" \
  -d '{
    "model": "jimeng-4.0",
    "messages": [
      {
        "role": "user",
        "content": "画一幅山水画"
      }
    ]
  }'
```

## 🔍 API响应格式

### 图像生成响应
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

### 聊天完成响应
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

### 流式响应 (SSE)
```
data: {"id":"chatcmpl-123","object":"chat.completion.chunk","created":1759058768,"model":"jimeng-4.0","choices":[{"index":0,"delta":{"role":"assistant","content":"🎨 图像生成中，请稍候..."},"finish_reason":null}]}

data: {"id":"chatcmpl-123","object":"chat.completion.chunk","created":1759058768,"model":"jimeng-4.0","choices":[{"index":1,"delta":{"role":"assistant","content":"![image](https://example.com/image.jpg)"},"finish_reason":"stop"}]}

data: [DONE]
```

## 🏗️ 项目架构

```
jimeng-api/
├── src/
│   ├── api/
│   │   ├── controllers/          # 控制器层
│   │   │   ├── core.ts          # 核心功能（网络请求、文件处理）
│   │   │   ├── images.ts        # 图像生成逻辑
│   │   │   ├── videos.ts        # 视频生成逻辑
│   │   │   └── chat.ts          # 聊天接口逻辑
│   │   ├── routes/              # 路由定义
│   │   └── consts/              # 常量定义
│   ├── lib/                     # 核心库
│   │   ├── configs/            # 配置加载
│   │   ├── consts/             # 常量
│   │   ├── exceptions/         # 异常类
│   │   ├── interfaces/         # 接口定义
│   │   ├── request/            # 请求处理
│   │   ├── response/           # 响应处理
│   │   ├── config.ts           # 配置中心
│   │   ├── server.ts           # 服务器核心
│   │   ├── logger.ts           # 日志记录器
│   │   ├── error-handler.ts    # 统一错误处理
│   │   ├── smart-poller.ts     # 智能轮询器
│   │   └── aws-signature.ts    # AWS签名
│   ├── daemon.ts               # 守护进程
│   └── index.ts               # 入口文件
├── configs/                    # 配置文件
├── Dockerfile                 # Docker配置
└── package.json              # 项目配置
```

## 🔧 核心组件

### 智能轮询器 (SmartPoller)
- 基于状态码自适应调整轮询间隔
- 多重退出条件，避免无效等待
- 详细的进度跟踪和日志记录

### 统一错误处理 (ErrorHandler)
- 分类错误处理（网络错误、API错误、超时等）
- 自动重试机制
- 用户友好的错误提示

### 安全JSON解析
- 自动修复常见JSON格式问题
- 支持尾随逗号、单引号等非标准格式
- 详细的解析错误日志


## ⚙️ 高级配置

### 轮询配置
```typescript
export const POLLING_CONFIG = {
  MAX_POLL_COUNT: 900,    // 最大轮询次数 (15分钟)
  POLL_INTERVAL: 1000,    // 基础轮询间隔 (1秒)
  STABLE_ROUNDS: 5,       // 稳定轮次
  TIMEOUT_SECONDS: 900    // 超时时间 (15分钟)
};
```

### 重试配置
```typescript
export const RETRY_CONFIG = {
  MAX_RETRY_COUNT: 3,     // 最大重试次数
  RETRY_DELAY: 5000       // 重试延迟 (5秒)
};
```

## 🐛 故障排除

### 常见问题

1. **JSON解析错误**
   - 检查请求体格式是否正确
   - 系统会自动修复常见格式问题

2. **Sessionid失效**
   - 重新获取对应站点的Sessionid
   - 检查Sessionid格式是否正确

3. **生成超时**
   - 图像生成：通常1-3分钟
   - 视频生成：通常3-15分钟
   - 系统会自动处理超时情况

4. **积分不足**
   - 前往即梦/dreamina官网查看积分余额
   - 系统会提供详细的积分状态信息

## 🙏 致谢

本项目基于以下开源项目的贡献和启发：

- **[jimeng-free-api-all](https://github.com/wwwzhouhui/jimeng-free-api-all)** - 感谢该项目为即梦API逆向工程提供的重要参考和技术基础，本项目在其基础上进行了功能完善和架构优化

## 📄 许可证

GPL v3 License - 详见 [LICENSE](LICENSE) 文件

## ⚠️ 免责声明

本项目仅供学习和研究使用，请遵守相关服务条款和法律法规。使用本项目所产生的任何后果由使用者自行承担。
