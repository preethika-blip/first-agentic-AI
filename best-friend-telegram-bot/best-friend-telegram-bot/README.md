# Best Friend Telegram Bot

An n8n workflow that turns a Telegram bot into "Buddy", a warm, funny best-friend chatbot powered by Groq (`llama-3.3-70b-versatile`). Every Telegram chat gets its own memory (last 10 interactions).

## How it works

```
Telegram Trigger -> Has text? --true--> Buddy Agent -> Send Reply
                              \-false-> Reply Text Only
                    Groq Chat Model  --> Buddy Agent (model)
                    Simple Memory    --> Buddy Agent (memory, keyed by chat id)
```

- **Telegram Trigger**: listens for `message` updates.
- **Has text?**: routes text messages to the AI agent; stickers, photos, etc. get a friendly "text only" reply.
- **Buddy Agent**: n8n AI Agent with a best-friend system prompt. Replies in the user's language (including Hinglish/Tenglish), keeps messages short, and switches to a caring tone if the user mentions self-harm or danger.
- **Groq Chat Model**: `llama-3.3-70b-versatile`, temperature 0.8.
- **Simple Memory**: window buffer of 10, session key = Telegram chat id.
- **Send Reply**: plain text, no parse mode, n8n attribution turned off.
- The agent node retries up to 3 times with a 2 second wait.

## Setup

1. In n8n, go to **Workflows -> Import from File** and choose `workflow.json`.
2. Create credentials if you don't have them yet:
   - **Telegram API**: bot token from [@BotFather](https://t.me/BotFather).
   - **Groq API**: key from [console.groq.com](https://console.groq.com).
3. Open these nodes and select your credentials:
   - Telegram Trigger, Send Reply, Reply Text Only -> your Telegram credential
   - Groq Chat Model -> your Groq credential
4. Toggle the workflow to **Active**.

The Telegram trigger needs your n8n instance to be reachable over a public HTTPS URL. n8n Cloud has this by default. When self-hosting, set the `WEBHOOK_URL` environment variable to your public HTTPS address (reverse proxy or a tunnel such as ngrok) and restart n8n.

## Test

1. Open your bot in Telegram and send `/start`, then `hi`. Expect a friendly reply.
2. Send a sticker. Expect: "I can only read text messages for now, bro".

## Security

No API keys or tokens are stored in this repository. Credentials are selected inside n8n after import.
