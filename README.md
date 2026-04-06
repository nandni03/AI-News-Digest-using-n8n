# 🧠 AI News Digest — n8n Workflow

An automated AI news digest bot that delivers the top 5 daily AI headlines — summarized using a Hugging Face NLP model — straight to your Telegram. Trigger it on demand by messaging your bot, or let it run automatically every morning at 8 AM.

---

## 📋 Overview

This n8n workflow supports **two trigger modes**:

| Mode | How it works |
|------|-------------|
| ⏰ **Scheduled** | Runs automatically every day at 8:00 AM |
| 💬 **On-demand** | Send any message to your Telegram bot to trigger instantly |

It fetches the top 5 AI articles from GNews, summarizes each using Facebook's `bart-large-cnn` model via Hugging Face, and sends a clean formatted digest to Telegram.

---

## 🔄 Workflow Diagram

```
💬 Telegram Trigger          ⏰ Schedule Trigger
  (any user message)            (8AM daily)
         │                            │
         └────────────┬───────────────┘
                      ▼
             🌐 GNews API Fetch
               (Top 5 AI articles)
                      │
                      ▼
            ⚙️ Code (JavaScript)
           Extract title, desc, URL
                      │
          ┌───────────┴───────────┐
          ▼                       ▼
       🔀 Merge               🤖 Hugging Face
      (Input 0)             BART Summarization
                                  │
                                  ▼
                              🔀 Merge
                             (Input 1)
                                  │
                                  ▼
                       ⚙️ Code (JavaScript)
                        Format digest message
                                  │
                                  ▼
                         📱 Telegram Bot
                         Send daily digest
```

---

## ✨ Features

- 💬 **On-demand trigger** — message your bot anytime to get the latest digest
- ⏰ **Scheduled trigger** — auto-runs every morning at 8:00 AM
- 📰 **Top 5 AI articles** fetched fresh from GNews
- 🤖 **AI summarization** using Facebook's `bart-large-cnn` via Hugging Face Inference API
- 📱 **Telegram delivery** — formatted digest sent directly to your chat
- 🔧 **Modular design** — swap news topics, models, or output channels easily

---

## 🧩 Nodes Used

| Node | Type | Purpose |
|------|------|---------|
| `Telegram Trigger` | Trigger | Listens for incoming messages (polling mode) |
| `Schedule Trigger` | Trigger | Fires daily at 8:00 AM via cron |
| `HTTP Request` | Action | Fetches top AI articles from GNews API |
| `Code (JavaScript)` | Action | Parses and structures article data |
| `HTTP Request1` | Action | Summarizes articles via Hugging Face BART |
| `Merge` | Logic | Combines article data with summaries |
| `Code (JavaScript1)` | Action | Formats the final Telegram message |
| `Send a text message` | Action | Sends the digest via Telegram bot |

---

## 🛠️ Setup Instructions

### Prerequisites

- [n8n](https://n8n.io/) instance (self-hosted or cloud)
- A [GNews API key](https://gnews.io/) (free tier available)
- A [Hugging Face API token](https://huggingface.co/settings/tokens)
- A Telegram Bot token (via [@BotFather](https://t.me/botfather))
- Your Telegram Chat ID

---

### Step 1 — Create Your Telegram Bot

1. Open Telegram and message **@BotFather**
2. Send `/newbot` and follow the prompts
3. Copy your **Bot Token** — it looks like:
   ```
   7123456789:AAFxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
   ```
4. To get your **Chat ID**, paste this in your browser (replace with your token):
   ```
   https://api.telegram.org/botYOUR_BOT_TOKEN/getUpdates
   ```
   Send your bot a message first, then look for `"chat":{"id":...}` in the response.

---

### Step 2 — Import the Workflow

1. Download [`AI_news_Digest.json`](./AI_news_Digest.json)
2. Open your n8n instance
3. Go to **Workflows → Import from File**
4. Select the downloaded JSON

---

### Step 3 — Configure Credentials

#### Telegram API
1. Go to **Settings → Credentials → Add Credential → Telegram API**
2. Paste your **Bot Token** and save
3. Link this credential to both the **Telegram Trigger** and **Send a text message** nodes

#### GNews API
In the **HTTP Request** node, replace the API key in the URL:
```
https://gnews.io/api/v4/search?q=artificial%20intelligence&lang=en&max=5&apikey=YOUR_GNEWS_API_KEY
```

#### Hugging Face
In the **HTTP Request1** node, update the Authorization header:
```
Bearer YOUR_HUGGINGFACE_API_TOKEN
```

#### Chat ID
In the **Send a text message** node, set the Chat ID to your Telegram Chat ID.

---

### Step 4 — Activate & Test

1. Toggle the workflow to **Active**
2. Open Telegram and send **any message** to your bot
3. The digest should arrive within ~60 seconds (polling interval)
4. It will also auto-run every day at **8:00 AM** ✅

---

## 📬 Sample Output

```
🧠 Daily AI News Digest

1. OpenAI Announces New Research Milestone
Researchers at OpenAI have developed a new model capable of...
Read more: https://example.com/article1

2. Google DeepMind Publishes Breakthrough Paper
A new study from DeepMind explores multi-modal reasoning...
Read more: https://example.com/article2

...
```

---

## ⚙️ Customization

| What to change | Where |
|----------------|-------|
| News topic | GNews URL `q=` parameter (e.g. `machine+learning`) |
| Number of articles | GNews URL `max=` parameter |
| Trigger time | Schedule Trigger cron expression |
| Summarization model | Hugging Face model URL |
| Output channel | Replace Telegram node with Slack, Email, Discord, etc. |

---

## 📦 Tech Stack

- **[n8n](https://n8n.io/)** — Workflow automation
- **[GNews API](https://gnews.io/)** — News data source
- **[Hugging Face Inference API](https://huggingface.co/inference-api)** — NLP summarization
- **[facebook/bart-large-cnn](https://huggingface.co/facebook/bart-large-cnn)** — Summarization model
- **[Telegram Bot API](https://core.telegram.org/bots/api)** — Message delivery & trigger

---

## 🔍 How the Telegram Trigger Works

This workflow uses **polling mode** — n8n periodically checks for new messages from your bot. This means:

- ✅ No public URL or webhook required
- ✅ Works on local/free-tier n8n instances
- ⚠️ Response may take up to ~60 seconds after you message the bot
- ✅ Any message sent to the bot triggers the digest

---

## ⚠️ Known Limitations (Free Tier)

| Issue | Cause | Workaround |
|-------|-------|-----------|
| Slow/no summary | Hugging Face free tier times out | Retry or use article description as fallback |
| ~60s trigger delay | Polling interval | Expected on free tier — upgrade for webhooks |
| 100 requests/day | GNews free tier limit | More than enough for daily use |

---

## 📄 License

MIT License — free to use, modify, and share.

---

## 🙋 Author

Built with ❤️ using n8n. Contributions and suggestions welcome!
