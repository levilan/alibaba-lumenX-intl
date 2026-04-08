<div align="center">
  <img src="docs/images/LumenX Studio Banner.jpeg" alt="LumenX Studio Banner" width="100%" />
</div>

<div align="center">

# LumenX Studio

### AI-Native Motion Comic Creation Platform
**Render Noise into Narrative**

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Python](https://img.shields.io/badge/python-3.11%2B-blue)](https://www.python.org/)
[![Node](https://img.shields.io/badge/node-20%2B-green)](https://nodejs.org/)
[![Docker](https://img.shields.io/badge/docker-ready-2496ED?logo=docker)](https://www.docker.com/)

[English](README.md) | [中文](README_CN.md) | [User Manual](USER_MANUAL.md)

</div>

---

LumenX Studio is an **all-in-one AI motion comic production platform** that transforms novel text into dynamic videos — from script analysis and character design to storyboard generation and video synthesis — in a single streamlined workflow.

Powered by Alibaba Cloud Qwen (reasoning) + Wanx (visual generation).

---

## ✨ Key Features

| Step | Feature |
|------|---------|
| 1 Script | LLM-based extraction of characters, scenes & props |
| 2 Art Direction | Custom visual style with LoRA / style prompts |
| 3 Assets | AI-generated characters, scenes & props (with turnarounds) |
| 4 Storyboard | Drag-and-drop shot editor with WYSIWYG composition |
| 5 Motion | Image-to-Video & Reference-to-Video generation |
| 6 Assembly | Select best takes & one-click merge |
| 7 Voice | Character TTS dubbing |
| 8 Final Mix | Multi-track audio preview (video + dialogue + SFX + BGM) |
| 9 Export | Export final merged video |

---

## 🚀 Quick Start — Docker (Recommended)

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/) + [Docker Compose](https://docs.docker.com/compose/install/)
- An **Alibaba Cloud DashScope API Key** ([get one here](https://dashscope.aliyun.com/))

### 1. Clone the repository

```bash
git clone https://github.com/levilan/alibaba-lumenX-intl.git
cd alibaba-lumenX-intl
```

### 2. Configure environment

```bash
cp .env.example .env
```

Open `.env` and fill in your keys — **minimum required**:

```env
DASHSCOPE_API_KEY=sk-xxxxxxxxxxxxxxxxxxxxxxxx
```

For international deployments (outside Mainland China), also set:

```env
DASHSCOPE_BASE_URL=https://dashscope-intl.aliyuncs.com
DASHSCOPE_WEBSOCKET_URL=wss://dashscope-intl.aliyuncs.com/api-ws/v1/inference
```

See **.env.example** for all available options (OSS, Kling, Vidu, alternative LLMs).

### 3. Build and start

```bash
docker compose up --build
```

First build takes ~5 minutes (downloads dependencies). On subsequent starts:

```bash
docker compose up
```

### 4. Open the app

| Service | URL |
|---------|-----|
| **Frontend** | http://localhost:3000 |
| **Backend API** | http://localhost:17177 |
| **Swagger Docs** | http://localhost:17177/docs |

---

## ⚙️ Environment Variables

### Required

| Variable | Description |
|----------|-------------|
| `DASHSCOPE_API_KEY` | Alibaba Cloud Model Studio API key |

### Optional — Cloud Storage (OSS)

| Variable | Description |
|----------|-------------|
| `ALIBABA_CLOUD_ACCESS_KEY_ID` | Alibaba Cloud access key ID |
| `ALIBABA_CLOUD_ACCESS_KEY_SECRET` | Alibaba Cloud access key secret |
| `OSS_BUCKET_NAME` | OSS bucket name |
| `OSS_ENDPOINT` | e.g. `oss-ap-southeast-1.aliyuncs.com` |
| `OSS_BASE_PATH` | Path prefix inside bucket (default: `comic_gen/`) |

> Without OSS, all generated media is stored locally in the `output/` directory.

### Optional — Alternative LLM Providers

```env
# Use OpenAI / DeepSeek / Ollama instead of DashScope for reasoning:
LLM_PROVIDER=openai
OPENAI_API_KEY=your_key
OPENAI_BASE_URL=https://api.openai.com/v1
OPENAI_MODEL=gpt-4o
```

### Optional — Additional Video Providers

| Variable | Description |
|----------|-------------|
| `KLING_PROVIDER_MODE` | `dashscope` (default) or `vendor` |
| `KLING_ACCESS_KEY` | Required only when `KLING_PROVIDER_MODE=vendor` |
| `KLING_SECRET_KEY` | Required only when `KLING_PROVIDER_MODE=vendor` |
| `VIDU_PROVIDER_MODE` | `dashscope` (default) or `vendor` |
| `VIDU_API_KEY` | Required only when `VIDU_PROVIDER_MODE=vendor` |

---

## 🗂️ Project Structure

```
alibaba-lumenX-intl/
├── src/                     # Python backend (FastAPI)
│   ├── apps/comic_gen/      # Core pipeline: API, models, LLM, video, audio
│   ├── models/              # AI model integrations (Wanx, Kling, Vidu, Doubao)
│   └── utils/               # Shared utilities (OSS, provider registry, etc.)
├── frontend/                # Next.js 14 frontend (React + TypeScript + Tailwind)
│   └── src/components/      # UI modules per workflow step
├── docker/
│   └── nginx.conf           # Nginx config (serves frontend + proxies API)
├── output/                  # Generated media (auto-created, git-ignored)
├── Dockerfile.backend        # Python backend image
├── Dockerfile.frontend       # Next.js static build → nginx image
├── docker-compose.yml        # Orchestrates backend + frontend
├── requirements-docker.txt   # Python deps for Docker (no pywebview)
├── .env.example              # Environment variable template
└── bin/ffmpeg                # Bundled FFmpeg binary (used by backend)
```

---

## 🛠️ Local Development (Without Docker)

### Backend

```bash
# Python 3.11+ required
python -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate
pip install -r requirements.txt

cp .env.example .env              # Fill in DASHSCOPE_API_KEY
./start_backend.sh                # Starts on http://localhost:17177
```

### Frontend

```bash
cd frontend
npm install --legacy-peer-deps
npm run dev                       # Starts on http://localhost:3000
```

---

## 🏗️ Architecture

```
Browser
  │
  ├── Port 3000 → nginx (frontend static files)
  │                  │
  │              API proxy → Port 17177 → FastAPI backend
  │                               │
  │                          output/ (local files)
  │                          Alibaba Cloud DashScope (AI)
  │                          OSS (optional media storage)
```

**Tech Stack:**
- **Frontend**: Next.js 14 · React 18 · TypeScript · Tailwind CSS · Three.js
- **Backend**: FastAPI · Python 3.11+ · Uvicorn
- **AI Core**: Qwen (LLM reasoning) + Wanx (image/video generation)
- **Video**: FFmpeg

---

## 📖 Documentation

- [User Manual](USER_MANUAL.md) — step-by-step workflow guide
- [API Docs](http://localhost:17177/docs) — Swagger UI (when running)

---

## 🤝 Contributing

Issues and PRs are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## 📄 License

[MIT License](LICENSE) — © Alibaba Group
