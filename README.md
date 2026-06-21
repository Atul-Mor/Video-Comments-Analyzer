# 📊 YouTube Comment Intelligence

An automated pipeline that fetches YouTube comments, analyzes them using a local AI model, and presents insights through a live dashboard — all built with n8n, Ollama, and MySQL.

---

## What It Does

- Fetches all comments from any YouTube video via the YouTube Data API
- Analyzes each comment using **Llama3 (Ollama)** running locally — no paid API needed
- Classifies comments by sentiment, category, keywords, complaints, and requests
- Presents insights as a live HTML dashboard via a single webhook URL

---

## Architecture

```
Webhook (video ID)
    │
    ├── Workflow 1: Fetch & Store
    │       └── YouTube API → MySQL (comments table)
    │
    ├── Workflow 2: Analyze
    │       └── Unprocessed comments → Llama3 → MySQL (analysis table)
    │
    ├── Workflow 3: Final Aggregation
    │       └── SQL queries → Insights JSON
    │
    └── Respond to Webhook → HTML Dashboard
```

---

## Prerequisites

| Tool | Version | Purpose |
|------|---------|---------|
| [n8n](https://n8n.io) | Latest | Workflow automation |
| [Ollama](https://ollama.com) | Latest | Local AI model runner |
| [MySQL](https://www.mysql.com) | 8.0+ | Database |
| Node.js | 18+ | Required by n8n |

---

## Setup Guide

### 1. Install n8n

```bash
npm install -g n8n
```

Start n8n:

```bash
n8n start
```

Open in browser: `http://localhost:5678`

---

### 2. Install Ollama and Llama3

Download Ollama from [https://ollama.com](https://ollama.com), then pull the model:

```bash
ollama pull llama3
```

Start Ollama (runs on port 11434 by default):

```bash
ollama serve
```

Verify it works:

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "llama3",
  "prompt": "reply with the word hello only",
  "stream": false
}'
```

---

### 3. Set Up MySQL Database

Log into MySQL and create the database and tables:

```sql
CREATE DATABASE yt_insights;
USE yt_insights;

CREATE TABLE comments (
  id VARCHAR(100) PRIMARY KEY,
  videoId VARCHAR(50),
  channelId VARCHAR(100),
  text TEXT,
  authorName VARCHAR(255),
  authorChannelId VARCHAR(100),
  likeCount INT DEFAULT 0,
  totalReplyCount INT DEFAULT 0,
  publishedAt DATETIME,
  processed INT DEFAULT 0
);

CREATE TABLE analysis (
  commentId VARCHAR(100) PRIMARY KEY,
  videoId VARCHAR(50),
  sentiment_label VARCHAR(20),
  topics JSON,
  is_complaint TINYINT(1) DEFAULT 0,
  is_request TINYINT(1) DEFAULT 0,
  category VARCHAR(100),
  subtopic VARCHAR(255),
  keywords JSON,
  created_at DATETIME
);
```

---

### 4. Get a YouTube Data API Key

1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. Create a new project
3. Enable **YouTube Data API v3**
4. Create credentials → **API Key**
5. Copy the key

---

### 5. Configure n8n Credentials

In n8n, add the following credentials:

**MySQL**
- Host: `localhost`
- Port: `3306`
- Database: `yt_insights`
- User: your MySQL username
- Password: your MySQL password

**HTTP Header Auth (YouTube API)**
- Used in the HTTP Request node for YouTube API calls
- Add your API key as a query parameter: `key = YOUR_API_KEY`

**Ollama (via HTTP Request)**
- Base URL: `http://localhost:11434`
- No auth required

---

### 6. Import Workflows

1. Open n8n at `http://localhost:5678`
2. Go to **Workflows → Import**
3. Import these 4 workflow JSON files in order:
   - `workflow1_fetch_store.json`
   - `workflow2_analyze.json`
   - `workflow3_aggregation.json`
   - `workflow4_webhook.json`
4. Activate all workflows using the toggle in the top right of each

---

### 7. Configure Webhook

In **Workflow 4 (Webhook Orchestrator)**:

- The webhook node generates a URL like:
  `http://localhost:5678/webhook/youtube-insights`
- Open the Webhook node to copy your exact URL

---

## Usage

Send a GET request with a YouTube video ID:

```
http://localhost:5678/webhook/youtube-insights?videoId=VIDEO_ID_HERE
```

Or simply open it in your browser. Example:

```
http://localhost:5678/webhook/youtube-insights?videoId=dQw4w9WgXcQ
```

The system will:
1. Fetch all comments (skips if already fetched)
2. Analyze unprocessed comments in batches of 5
3. Return a live HTML dashboard with insights

---

## Dashboard Preview

The response is an HTML page showing:

- Sentiment distribution (donut chart)
- Sentiment breakdown (progress bars)
- Top keywords (tag cloud)
- Top complaints (horizontal bar chart)
- Top requests (horizontal bar chart)
- Most engaged comments (scrollable feed)

---

## Project Structure

```
youtube-comment-intelligence/
│
├── workflows/
│   ├── workflow1_fetch_store.json
│   ├── workflow2_analyze.json
│   ├── workflow3_aggregation.json
│   └── workflow4_webhook.json
│
├── sql/
│   └── setup.sql
│
└── README.md
```

---

## How Each Workflow Works

### Workflow 1 — Fetch & Store
- Receives `videoId` from the orchestrator
- Calls YouTube Data API with pagination support
- Inserts comments into the `comments` table
- Skips if comments for that video already exist

### Workflow 2 — Analyze
- Fetches unprocessed comments (`processed = 0`) in batches of 5
- Sends each batch to Llama3 via Ollama
- Parses AI output (handles malformed JSON automatically)
- Stores results in `analysis` table
- Marks each comment as `processed = 1`

### Workflow 3 — Aggregation
- Runs parallel SQL queries for:
  - Sentiment distribution
  - Top keywords
  - Top complaints
  - Top requests
  - Most engaged comments
- Merges results into a single JSON object

### Workflow 4 — Orchestrator (Webhook)
- Entry point for the entire system
- Checks if video ID is provided
- Checks if comments already exist
- Checks if analysis already exists
- Calls only the workflows needed
- Returns the HTML dashboard

---

## Troubleshooting

| Issue | Fix |
|-------|-----|
| Ollama not responding | Run `ollama serve` in terminal |
| YouTube API quota exceeded | Wait 24 hours or use a different API key |
| JSON parse errors in analysis | Already handled — bad AI outputs are skipped automatically |
| Webhook returns empty dashboard | Check that Workflow 3 is activated and MySQL has data |
| `processed` not updating | Ensure the Update node has **Execute Once Per Item** enabled in Settings |
| n8n sub-workflows not waiting | Enable **Wait for sub-workflow to complete** in each Call Workflow node |

---

## Tech Stack

- **n8n** — Visual workflow automation
- **Ollama + Llama3** — Free local AI model for comment analysis
- **MySQL** — Relational database for comments and analysis
- **YouTube Data API v3** — Comment fetching
- **Chart.js** — Dashboard visualizations
- **HTML/CSS** — Dashboard frontend served via webhook response

---

## Author

Built as part of a project exploring AI-powered automation pipelines using local LLMs and no-code/low-code tools.
