# DolphinBot (Discord + Local LLM)

A Discord chatbot that connects to your **local LLM backend** (OpenAI-compatible API), with slash commands, conversational replies, cooldown behavior, image handling, channel locking, memory, favorites, and idle chat prompts.

## What this bot can do
- Respond to `/chat` slash command (admin-only).
- Respond to normal messages in chat (conversational mode).
- Keep short rolling context memory per channel (10 entries).
- Enforce one in-flight request per channel.
- Enforce 30-second response pacing (messages are delayed, not dropped).
- Optionally lock usage to one channel (`/lockchannel`).
- Manage favorites manually (`/favorite`) and auto-pick favorites based on activity.
- Attempt to analyze images from Discord attachments (with compatibility fallback).
- If a channel goes quiet, send a model-generated idle message every hour.

## Commands
- `/chat prompt:<text> [system:<text>]`
  - Sends prompt to your local model.
  - Admin-only.
- `/lockchannel`
  - Locks bot usage to the current channel.
  - Admin-only.
- `/favorite action:add user:@user`
- `/favorite action:remove user:@user`
- `/favorite action:list`
  - Manage/list manual and auto favorites.
  - Admin-only.

## Requirements
- Python 3.11+ (Windows)
- Discord bot token
- LM Studio installed
- A vision-capable model if you want image analysis

## Beginner-friendly setup (Windows + LM Studio)

### 1) Install Python
1. Open https://www.python.org/downloads/windows/
2. Download Python 3.11 or newer.
3. Run installer.
4. **Important:** check **“Add Python to PATH”** during install.
5. Finish install.

### 2) Install LM Studio
1. Go to https://lmstudio.ai/
2. Download and install LM Studio for Windows.
3. Open LM Studio.
4. Download your model (for example, your current Gemma VL model).
5. Go to the local server/API section in LM Studio.
6. Start the server in **OpenAI-compatible mode**.
7. Confirm endpoint looks like:
   - `http://127.0.0.1:1234/v1` (or whichever host/port LM Studio shows).

### 3) Put project files in a folder
Example folder:
- `C:\Users\YOUR_NAME\blackbot\`

Make sure these files are present:
- `bot.py`
- `requirements.txt`
- `.env.example`
- `README.md`
- `SETUP.md`

### 4) Open terminal in project folder
Use Command Prompt or PowerShell:
1. Press Start, type `cmd`, open Command Prompt.
2. Run:
   - `cd C:\Users\YOUR_NAME\blackbot`

### 5) Create virtual environment
In the project folder:
1. `python -m venv .venv`
2. Activate it:
   - Command Prompt: `.venv\Scripts\activate`
   - PowerShell: `.venv\Scripts\Activate.ps1`

If activation is successful, you should see `(.venv)` at line start.

### 6) Install dependencies
Run:
- `python -m pip install -r requirements.txt`

### 7) Configure environment variables
1. Copy example file:
   - Command Prompt: `copy .env.example .env`
   - PowerShell: `Copy-Item .env.example .env`
2. Open `.env` in Notepad and edit values:

```env
DISCORD_BOT_TOKEN=your_discord_bot_token_here
LLM_BASE_URL=http://127.0.0.1:1234/v1
LLM_API_KEY=dummy-key
LLM_MODEL=your_model_name_from_lm_studio
```

Notes:
- `LLM_BASE_URL` must include `http://` or `https://` and usually `/v1`.
- `LLM_MODEL` must match model name your backend exposes.
- `LLM_API_KEY` can be dummy if your local server ignores it.

### 8) Discord bot setup
1. Go to Discord Developer Portal:
   - https://discord.com/developers/applications
2. Create/select your app and bot.
3. Copy bot token into `.env`.
4. Enable **Message Content Intent** (required for conversational mode).
5. Invite bot to your server with scopes:
   - `bot`
   - `applications.commands`
6. Ensure it can read and send messages in your target channel.

### 9) Run the bot
In activated virtual environment:
- `python bot.py`

If successful, terminal shows login and command sync logs.

### 10) First-use checks
1. In Discord, try `/chat prompt:hello`.
2. Send a normal text message in the same channel.
3. If using channel lock, run `/lockchannel` in target channel.
4. For image test, upload image and send a message with/without caption.

## Configuration reference (.env)
- `DISCORD_BOT_TOKEN`
  - Discord bot token.
- `LLM_BASE_URL`
  - OpenAI-compatible base URL, e.g. `http://127.0.0.1:1234/v1`.
- `LLM_API_KEY`
  - Sent as Bearer token. Can be dummy if backend allows it.
- `LLM_MODEL`
  - Exact model ID/name that backend exposes.

## Behavior details
- Cooldown:
  - Bot waits between responses (30 seconds pacing) and then replies.
- Memory:
  - Keeps recent conversation memory (last 10 stored turns/events in channel memory queue).
- Idle messages:
  - If chat is inactive for 1 hour, bot posts a random model-generated message.
- Image support:
  - Tries OpenAI-style multimodal request.
  - Retries with data URL conversion if needed.
  - Falls back gracefully to text-only interpretation if backend rejects image schema.

## Troubleshooting
- `ModuleNotFoundError: No module named 'discord'`
  - You are likely not in virtual env. Activate `.venv` and run again.
- `Request URL is missing http:// or https://`
  - Fix `LLM_BASE_URL` format in `.env`.
- `400 Bad Request` on image
  - Model/backend schema mismatch. Confirm LM Studio server is OpenAI-compatible and model supports vision.
- Bot does not answer normal chat
  - Enable **Message Content Intent** in Discord Developer Portal.
- Slash commands not showing
  - Wait a minute, re-invite with `applications.commands`, or restart bot.

## Security note
- Keep `.env` private.
- Never share your Discord bot token publicly.
- Rotate token immediately if exposed.

