<div align="center">
  <img src="docs/images/LumenX Studio Banner.jpeg" alt="LumenX Studio Banner" width="100%" />
</div>

<div align="center">

# LumenX Studio

### AI 原生動態漫畫創作平台
**將文字雜訊，淬煉成敘事影像**

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Python](https://img.shields.io/badge/python-3.11%2B-blue)](https://www.python.org/)
[![Node](https://img.shields.io/badge/node-20%2B-green)](https://nodejs.org/)
[![Docker](https://img.shields.io/badge/docker-ready-2496ED?logo=docker)](https://www.docker.com/)

[English](README.md) | [中文](README_CN.md) | [使用手冊](USER_MANUAL.md)

</div>

---

LumenX Studio 是一套**一站式 AI 動態漫畫製作平台**，能將小說文本自動轉化為動態影片——從劇本分析、角色設計，到分鏡生成與影片合成，全流程一氣呵成。

核心模型驅動：**阿里雲 Qwen**（推理）+ **Wanx**（視覺生成）。

---

## 功能總覽

| 步驟 | 功能說明 |
|------|---------|
| 1 劇本分析 | LLM 自動提取人物、場景與道具 |
| 2 美術風格 | 自訂視覺風格（LoRA / 風格提示詞） |
| 3 素材生成 | AI 生成角色、場景與道具（含多角度轉身圖） |
| 4 分鏡編輯 | 拖曳式鏡頭編輯器，所見即所得構圖 |
| 5 動態生成 | 圖生影片（I2V）與參考影片生成（R2V） |
| 6 素材挑選 | 精選最佳片段，一鍵合併 |
| 7 配音合成 | 角色 TTS 語音配音 |
| 8 混音預覽 | 多軌音訊預覽（影像 + 對話 + 音效 + BGM） |
| 9 成品匯出 | 匯出最終合併影片 |

---

## 快速開始（Docker，推薦）

### 前置需求

- [Docker](https://docs.docker.com/get-docker/) + [Docker Compose](https://docs.docker.com/compose/install/)
- **阿里雲 DashScope API Key**（[申請連結](https://dashscope.aliyun.com/)）

### 1. 複製專案

```bash
git clone https://github.com/levilan/alibaba-lumenX-intl.git
cd alibaba-lumenX-intl
```

### 2. 設定環境變數

```bash
cp .env.example .env
```

開啟 `.env`，填入必要金鑰——**最低需求**：

```env
DASHSCOPE_API_KEY=sk-xxxxxxxxxxxxxxxxxxxxxxxx
```

若部署於中國大陸以外地區，請額外設定：

```env
DASHSCOPE_BASE_URL=https://dashscope-intl.aliyuncs.com
DASHSCOPE_WEBSOCKET_URL=wss://dashscope-intl.aliyuncs.com/api-ws/v1/inference
```

完整可用選項（OSS、Kling、Vidu、替代 LLM）請參閱 **.env.example**。

### 3. 建置並啟動

```bash
docker compose up --build
```

首次建置約需 5 分鐘（下載相依套件）。後續啟動只需：

```bash
docker compose up
```

### 4. 開啟應用程式

| 服務 | 網址 |
|------|------|
| **前端介面** | http://localhost:3000 |
| **後端 API** | http://localhost:17177 |
| **Swagger 文件** | http://localhost:17177/docs |

---

## 環境變數說明

### 必要設定

| 變數名稱 | 說明 |
|----------|------|
| `DASHSCOPE_API_KEY` | 阿里雲模型服務 API 金鑰 |

### 選用 — 雲端儲存（OSS）

| 變數名稱 | 說明 |
|----------|------|
| `ALIBABA_CLOUD_ACCESS_KEY_ID` | 阿里雲 Access Key ID |
| `ALIBABA_CLOUD_ACCESS_KEY_SECRET` | 阿里雲 Access Key Secret |
| `OSS_BUCKET_NAME` | OSS Bucket 名稱 |
| `OSS_ENDPOINT` | 例如：`oss-ap-southeast-1.aliyuncs.com` |
| `OSS_BASE_PATH` | Bucket 內路徑前綴（預設：`comic_gen/`） |

> 未設定 OSS 時，所有生成媒體將儲存於本機 `output/` 目錄。

### 選用 — 替代 LLM 提供商

```env
# 使用 OpenAI / DeepSeek / Ollama 取代 DashScope 進行推理：
LLM_PROVIDER=openai
OPENAI_API_KEY=your_key
OPENAI_BASE_URL=https://api.openai.com/v1
OPENAI_MODEL=gpt-4o
```

### 選用 — 額外影片生成提供商

| 變數名稱 | 說明 |
|----------|------|
| `KLING_PROVIDER_MODE` | `dashscope`（預設）或 `vendor` |
| `KLING_ACCESS_KEY` | 僅在 `KLING_PROVIDER_MODE=vendor` 時需要 |
| `KLING_SECRET_KEY` | 僅在 `KLING_PROVIDER_MODE=vendor` 時需要 |
| `VIDU_PROVIDER_MODE` | `dashscope`（預設）或 `vendor` |
| `VIDU_API_KEY` | 僅在 `VIDU_PROVIDER_MODE=vendor` 時需要 |

---

## 專案結構

```
alibaba-lumenX-intl/
├── src/                     # Python 後端（FastAPI）
│   ├── apps/comic_gen/      # 核心流程：API、模型、LLM、影片、音訊
│   ├── models/              # AI 模型整合（Wanx、Kling、Vidu、Doubao）
│   └── utils/               # 共用工具（OSS、provider registry 等）
├── frontend/                # Next.js 14 前端（React + TypeScript + Tailwind）
│   └── src/components/      # 依工作流程步驟分組的 UI 元件
├── docker/
│   └── nginx.conf           # Nginx 設定（提供前端 + 代理 API）
├── output/                  # 生成媒體存放目錄（自動建立，已 git ignore）
├── Dockerfile.backend        # Python 後端映像
├── Dockerfile.frontend       # Next.js 靜態建置 → nginx 映像
├── docker-compose.yml        # 編排後端 + 前端
├── requirements-docker.txt   # Docker 用 Python 相依（不含 pywebview）
├── .env.example              # 環境變數範本
└── bin/ffmpeg                # 內附 FFmpeg 執行檔（供後端使用）
```

---

## 本機開發（不使用 Docker）

### 後端

```bash
# 需要 Python 3.11+
python -m venv venv
source venv/bin/activate          # Windows：venv\Scripts\activate
pip install -r requirements.txt

cp .env.example .env              # 填入 DASHSCOPE_API_KEY
./start_backend.sh                # 啟動於 http://localhost:17177
```

### 前端

```bash
cd frontend
npm install --legacy-peer-deps
npm run dev                       # 啟動於 http://localhost:3000
```

---

## 系統架構

```
瀏覽器
  │
  ├── Port 3000 → nginx（前端靜態檔案）
  │                  │
  │              API Proxy → Port 17177 → FastAPI 後端
  │                               │
  │                          output/（本機檔案）
  │                          阿里雲 DashScope（AI 服務）
  │                          OSS（選用媒體儲存）
```

**技術堆疊：**
- **前端**：Next.js 14 · React 18 · TypeScript · Tailwind CSS · Three.js
- **後端**：FastAPI · Python 3.11+ · Uvicorn
- **AI 核心**：Qwen（LLM 推理）+ Wanx（圖像 / 影片生成）
- **影片處理**：FFmpeg

---

## 文件資源

- [使用手冊](USER_MANUAL.md) — 逐步工作流程指南
- [API 文件](http://localhost:17177/docs) — Swagger UI（執行中才可存取）

---

## 貢獻指南

歡迎提交 Issue 與 PR，詳見 [CONTRIBUTING.md](CONTRIBUTING.md)。

---

## 授權條款

[MIT License](LICENSE) — © Alibaba Group
