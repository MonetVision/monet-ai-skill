# Monet AI Skill

[![API](https://img.shields.io/badge/API-monet.vision-blue)](https://monet.vision)
[![Documentation](https://img.shields.io/badge/docs-latest-green)](https://monet.vision/skills/keys)

Comprehensive AI content generation API designed for AI agents. Monet AI provides unified access to state-of-the-art AI generation models for video (Sora, Veo, Doubao Seedance, Wan, Hailuo, Kling), image (GPT-4o, Nano Banana, Seedream, Flux, Imagen, Ideogram), and music (MiniMax Music) generation. Build intelligent workflows that combine multiple AI capabilities for automated content creation pipelines.

## ✨ Features

### 🎬 Video Generation

- **Sora (OpenAI)**: OpenAI's latest video generation model with audio generation support
- **Veo (Google)**: Google's advanced video generation model with 1080p HD output
- **Doubao Seedance**: ByteDance's AI video model with excellent audio-visual sync
- **Wan**: Alibaba's video generation model with excellent localization support
- **Hailuo**: Fast video generation with good quality-speed balance
- **Kling**: Kuaishou's video generation model with strong visual realism

### 🖼️ Image Generation

- **GPT-4o**: OpenAI's multimodal model for accurate, photorealistic output
- **Nano Banana (Google)**: Ultra-high character consistency image generation
- **Seedream**: ByteDance's intelligent visual reasoning model
- **Wan**: Alibaba's visual model for high-quality and expressive images
- **Flux**: High-quality photorealistic and artistic image generation
- **Imagen**: Google's text-to-image model
- **Ideogram**: Specialized in text rendering and precise composition

### 🎵 Music Generation

- **MiniMax Music**: AI music generation with custom lyrics support and text-to-music conversion

## 🚀 Quick Start

### Get API Key

1. Visit [monet.vision](https://monet.vision) to register an account
2. After login, go to [API Keys page](https://monet.vision/skills/keys) to create an API Key
3. Configure the API Key in environment variables or code

### Setup

Set environment variable:

```bash
export MONET_API_KEY="monet_xxx"
```

### Your First Video Generation Task

```bash
curl -X POST https://monet.vision/api/v1/tasks/async \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $MONET_API_KEY" \
  -d '{
    "type": "video",
    "input": {
      "model": "sora-2",
      "prompt": "A cat running in the park",
      "duration": 5,
      "aspect_ratio": "16:9"
    },
    "idempotency_key": "unique-key-123"
  }'
```

> ⚠️ **Important**: `idempotency_key` is **required**. Use a unique value (e.g., UUID) to prevent duplicate task creation on retries.

**Response Example:**

```json
{
  "id": "task_abc123",
  "status": "pending",
  "type": "video",
  "created_at": "2026-02-27T10:00:00Z"
}
```

### Query Task Status

Task processing is asynchronous. You need to poll the task status until it becomes `success` or `failed`. **Recommended polling interval: 5 seconds**.

```bash
curl https://monet.vision/api/v1/tasks/task_abc123 \
  -H "Authorization: Bearer $MONET_API_KEY"
```

**Response when completed:**

```json
{
  "id": "task_abc123",
  "status": "success",
  "type": "video",
  "outputs": [
    {
      "model": "sora-2",
      "status": "success",
      "progress": 100,
      "url": "https://files.monet.vision/..."
    }
  ],
  "created_at": "2026-02-27T10:00:00Z",
  "updated_at": "2026-02-27T10:01:30Z"
}
```

### Polling Example (TypeScript)

```typescript
const TASK_ID = 'task_abc123';
const MONET_API_KEY = process.env.MONET_API_KEY;

async function pollTask() {
  while (true) {
    const response = await fetch(
      `https://monet.vision/api/v1/tasks/${TASK_ID}`,
      {
        headers: {
          Authorization: `Bearer ${MONET_API_KEY}`,
        },
      },
    );

    const data = await response.json();
    const status = data.status;

    if (status === 'success') {
      console.log('Task completed successfully!');
      console.log(JSON.stringify(data, null, 2));
      break;
    } else if (status === 'failed') {
      console.log('Task failed!');
      console.log(JSON.stringify(data, null, 2));
      break;
    } else {
      console.log(`Task status: ${status}, waiting...`);
      await new Promise((resolve) => setTimeout(resolve, 5000)); // Wait 5 seconds
    }
  }
}

pollTask();
```

## 📖 API Reference

### Create Task (Async)

**POST** `/api/v1/tasks/async`

Create an async task, returns task ID immediately.

### Create Task (Streaming)

**POST** `/api/v1/tasks/sync`

Create task with SSE streaming, waits for completion and streams progress.

```bash
curl -X POST https://monet.vision/api/v1/tasks/sync \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $MONET_API_KEY" \
  -N \
  -d '{
    "type": "video",
    "input": {
      "model": "sora-2",
      "prompt": "A cat running"
    },
    "idempotency_key": "unique-key-123"
  }'
```

### Get Task

**GET** `/api/v1/tasks/{taskId}`

Get task status and result.

### List Tasks

**GET** `/api/v1/tasks/list`

List tasks with pagination.

```bash
curl "https://monet.vision/api/v1/tasks/list?page=1&pageSize=20" \
  -H "Authorization: Bearer $MONET_API_KEY"
```

### Upload File

**POST** `/api/v1/files`

Upload a file to get an online access URL.

> 📁 **File Storage**: Uploaded files are stored for **24 hours** and will be automatically deleted after expiration.

```bash
curl -X POST https://monet.vision/api/v1/files \
  -H "Authorization: Bearer $MONET_API_KEY" \
  -F "file=@/path/to/your/file.mp4" \
  -v
```

**Use Cases:**

- Upload reference images for video/image generation tasks
- Upload video files for video processing
- Upload audio files for music tasks
- Get temporary online URLs for file sharing

## 🎨 Supported Models

### Video Generation Models

| Model                     | Provider  | Duration | Features                                   |
| ------------------------- | --------- | -------- | ------------------------------------------ |
| `sora-2`                  | OpenAI    | 10-15s   | Audio generation, reference image support  |
| `sora-2-pro`              | OpenAI    | 15-25s   | Cinematic quality, professional production |
| `veo-3-1`                 | Google    | 8s       | Advanced AI video with sound               |
| `veo-3-1-fast`            | Google    | 8s       | Ultra-fast video generation                |
| `wan-2-6`                 | Alibaba   | 5-15s    | Multi-shot, automatic audio                |
| `wan-2-5`                 | Alibaba   | 5-10s    | Automatic audio generation                 |
| `kling-2-6`               | Kuaishou  | 5-10s    | Cinematic videos and audio                 |
| `kling-2-5`               | Kuaishou  | 5-10s    | Smooth motion, strong consistency          |
| `hailuo-2-3`              | Hailuo    | 6-10s    | Excellent body movements and physics       |
| `doubao-seedance-1-5-pro` | ByteDance | 4-12s    | Pro-grade audio-visual sync                |

### Image Generation Models

| Model               | Provider          | Resolution | Features                           |
| ------------------- | ----------------- | ---------- | ---------------------------------- |
| `gpt-4o`            | OpenAI            | -          | Accurate, photorealistic output    |
| `gpt-image-1-5`     | OpenAI            | -          | True-color precision rendering     |
| `nano-banana-1-pro` | Google            | 1K-4K      | Google's flagship generation model |
| `nano-banana-2`     | Google            | 1K-4K      | Gemini latest model                |
| `wan-i-2-6`         | Alibaba           | -          | High-quality and expressive        |
| `seedream-5-0`      | ByteDance         | 2K-3K      | Intelligent visual reasoning       |
| `seedream-4-5`      | ByteDance         | 2K-4K      | 4K image model                     |
| `flux-2-dev`        | Black Forest Labs | -          | Photorealistic output              |
| `flux-kontext-pro`  | Black Forest Labs | -          | Perfect for editing, compositing   |
| `imagen-4-0`        | Google            | -          | Google's latest generation model   |
| `ideogram-v3`       | Ideogram          | -          | Outstanding design capabilities    |

### Music Generation Models

| Model           | Provider | Features                             |
| --------------- | -------- | ------------------------------------ |
| `minimax-music` | MiniMax  | Text-to-music, custom lyrics support |

For complete model list and detailed parameters, see [SKILL.md](SKILL.md).

## 💡 Usage Examples

### Example 1: Generate Video

```typescript
const response = await fetch('https://monet.vision/api/v1/tasks/async', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    Authorization: `Bearer ${process.env.MONET_API_KEY}`,
  },
  body: JSON.stringify({
    type: 'video',
    input: {
      model: 'sora-2',
      prompt: 'A cute kitten playing in a sunlit garden',
      duration: 10,
      aspect_ratio: '16:9',
    },
    idempotency_key: crypto.randomUUID(),
  }),
});

const task = await response.json();
console.log('Task created:', task.id);
```

### Example 2: Generate Image

```typescript
const response = await fetch('https://monet.vision/api/v1/tasks/async', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    Authorization: `Bearer ${process.env.MONET_API_KEY}`,
  },
  body: JSON.stringify({
    type: 'image',
    input: {
      model: 'gpt-4o',
      prompt: 'An illustration of a futuristic city with neon lights',
      aspect_ratio: '16:9',
    },
    idempotency_key: crypto.randomUUID(),
  }),
});

const task = await response.json();
console.log('Task created:', task.id);
```

### Example 3: Generate Music

```typescript
const response = await fetch('https://monet.vision/api/v1/tasks/async', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    Authorization: `Bearer ${process.env.MONET_API_KEY}`,
  },
  body: JSON.stringify({
    type: 'music',
    input: {
      model: 'minimax-music',
      prompt: 'Upbeat electronic music, suitable for background',
      lyrics: 'Optional custom lyrics',
    },
    idempotency_key: crypto.randomUUID(),
  }),
});

const task = await response.json();
console.log('Task created:', task.id);
```

### Example 4: Generate Video with Reference Image

```typescript
// First, upload reference image
const formData = new FormData();
formData.append('file', imageFile);

const uploadResponse = await fetch('https://monet.vision/api/v1/files', {
  method: 'POST',
  headers: {
    Authorization: `Bearer ${process.env.MONET_API_KEY}`,
  },
  body: formData,
});

const fileData = await uploadResponse.json();
const imageUrl = fileData.url;

// Then create video task
const response = await fetch('https://monet.vision/api/v1/tasks/async', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    Authorization: `Bearer ${process.env.MONET_API_KEY}`,
  },
  body: JSON.stringify({
    type: 'video',
    input: {
      model: 'sora-2',
      prompt: 'Based on this image, generate a dynamic video',
      images: [imageUrl],
      duration: 10,
      aspect_ratio: '16:9',
    },
    idempotency_key: crypto.randomUUID(),
  }),
});

const task = await response.json();
console.log('Task created:', task.id);
```

## 🔐 Authentication

All API requests require authentication via the `Authorization` header:

```
Authorization: Bearer monet_xxx
```

## 📋 Task Status

| Status       | Description                          |
| ------------ | ------------------------------------ |
| `pending`    | Task created, waiting for processing |
| `processing` | Task is being processed              |
| `success`    | Task completed successfully          |
| `failed`     | Task failed                          |

## 🌟 Best Practices

1. **Use Idempotency Keys**: Always provide a unique `idempotency_key` for tasks to prevent duplicate creation
2. **Reasonable Polling**: Poll task status every 5 seconds to avoid excessive requests
3. **Error Handling**: Implement proper error handling and retry logic
4. **File Management**: Remember uploaded files are only stored for 24 hours, use or save results promptly
5. **Choose the Right Model**: Select the most suitable model based on specific needs, balancing quality, speed, and cost

## 🤝 Support

- **Documentation**: [SKILL.md](SKILL.md)
- **Website**: [monet.vision](https://monet.vision)
- **API Keys**: [monet.vision/skills/keys](https://monet.vision/skills/keys)

## 📄 License

Please visit [monet.vision](https://monet.vision) for terms of service and usage license.

---

**Powered by Monet AI** - Comprehensive content generation platform built for AI agents
