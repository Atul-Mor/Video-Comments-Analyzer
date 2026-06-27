
# AI Workflow Automation for Audience Intelligence

> An end-to-end AI-powered workflow automation system built with **n8n**, **Ollama (Llama 3)**, **MySQL**, and **YouTube Data API v3** that automatically collects, analyzes, classifies, and visualizes large volumes of user feedback.

## Why This Project?

Organizations receive thousands of user comments daily. Reading them manually is slow and inconsistent.

This project automates the entire process:
- Collect comments
- Store them in a database
- Analyze them using AI
- Generate business insights
- Display results on a dashboard

Although this implementation uses YouTube comments, the same architecture can be reused for CRM conversations, WhatsApp chats, customer support tickets, product reviews, and survey responses.

---

## Business Applications

- Customer Feedback Analytics
- CRM Communication Monitoring
- WhatsApp Conversation Intelligence
- Product Review Analysis
- Social Media Monitoring
- Brand Reputation Tracking
- Voice of Customer Analytics

---

## Key Features

- Four modular n8n workflows
- AI-powered sentiment analysis
- Complaint detection
- Request detection
- Keyword extraction
- Category classification
- Local LLM processing with Ollama
- MySQL persistent storage
- REST API integrations
- Interactive HTML dashboard

---

## Architecture

```text
User
 |
Webhook
 |
Workflow 1 - Fetch Comments
 |
YouTube API
 |
MySQL
 |
Workflow 2 - AI Analysis
 |
Ollama (Llama3)
 |
Analysis Table
 |
Workflow 3 - Aggregate Insights
 |
Workflow 4 - Dashboard
 |
Interactive Analytics
```

---

## Workflow Overview

### Workflow 1 – Fetch & Store
- Receives Video ID
- Fetches comments from YouTube API
- Stores comments in MySQL

### Workflow 2 – AI Analysis
- Reads unprocessed comments
- Sends to Ollama (Llama3)
- Detects sentiment, complaints, requests, keywords and categories
- Stores structured results

### Workflow 3 – Aggregation
- Generates analytics
- Creates summarized insights

### Workflow 4 – Dashboard
- Orchestrates execution
- Returns interactive HTML dashboard

---

## Tech Stack

- n8n
- Ollama (Llama3)
- MySQL
- YouTube Data API v3
- HTML/CSS
- Chart.js
- JavaScript

---

## Skills Demonstrated

- Workflow Automation
- AI Integration
- Prompt Engineering
- REST APIs
- Database Design
- Data Pipelines
- System Design
- Backend Automation

---

## Repository Structure

```text
.
├── workflows/
├── sql/
├── README.md
├── HOW_TO_RUN.md
└── REPORT.pdf
```

---

## Future Improvements

- Multi-platform support
- WhatsApp integration
- CRM integration
- Email reporting
- Scheduled analytics
- Cloud deployment

---

## Documentation

For installation and execution instructions, see **HOW_TO_RUN.md**

---

## Author

**Atul Mor**

Built to explore AI-powered workflow automation using local LLMs, APIs, and modular orchestration.
