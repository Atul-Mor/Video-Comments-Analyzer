
# HOW_TO_RUN.md

## Prerequisites

- Node.js 18+
- n8n
- MySQL 8+
- Ollama
- YouTube Data API Key

---

## Install n8n

```bash
npm install -g n8n
```

Start n8n

```bash
n8n start
```

Open:

http://localhost:5678

---

## Install Ollama

Download Ollama and run

```bash
ollama pull llama3
ollama serve
```

Verify:

```bash
curl http://localhost:11434/api/generate
```

---

## Create MySQL Database

Import your SQL schema or execute the setup.sql file inside the sql folder.

---

## Configure Credentials

Configure:

- MySQL
- YouTube API Key
- Ollama Endpoint (http://localhost:11434)

---

## Import Workflows

Import all workflow JSON files:

1. workflow1_fetch_store.json
2. workflow2_analyze.json
3. workflow3_aggregation.json
4. workflow4_webhook.json

Activate all workflows.

---

## Run

Open:

http://localhost:5678/webhook/youtube-insights?videoId=VIDEO_ID

The system will

1. Fetch comments
2. Store comments
3. Analyze using Llama3
4. Generate insights
5. Display dashboard

---

## Troubleshooting

- Start Ollama if AI is unavailable.
- Verify MySQL connection.
- Check YouTube API quota.
- Activate all workflows.
- Ensure Execute Once Per Item is enabled where required.

