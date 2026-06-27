# DolphinBot Setup Guide

## 1) Prerequisites
- Python 3.11+ installed
- A Discord bot token
- A local OpenAI-compatible LLM endpoint (example: LM Studio, text-generation-webui, Ollama-compatible gateway, etc.)

## 2) Create and activate a virtual environment
### fish shell
```bash
python -m venv .venv
source .venv/bin/activate.fish
```

### bash/zsh
```bash
python -m venv .venv
source .venv/bin/activate
```

## 3) Install dependencies
```bash
python -m pip install -r requirements.txt
```

## 4) Configure environment variables
```bash
cp .env.example .env
```

Edit `.env` and set:
- `DISCORD_BOT_TOKEN`
- `LLM_BASE_URL` (must include protocol and `/v1`, e.g. `http://localhost:1234/v1`)
- `LLM_API_KEY` (dummy or real, still sent as Bearer token)
- `LLM_MODEL` (model name exposed by your backend)

## 5) Discord bot permissions/intents
- Enable **Message Content Intent** for the bot in Discord Developer Portal (required for conversational on-message mode).
- Invite the bot with scopes:
  - `bot`
  - `applications.commands`
- Ensure it can view/send messages in the target channel.

## 6) Run the bot
```bash
python bot.py
```

## 7) Verify
- Slash command: `/chat prompt:"hello"`
- Conversational mode: send a normal message in a channel the bot can read/write.

