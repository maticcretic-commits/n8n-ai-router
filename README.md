# n8n AI Router 🤖 — One Router to Run Them All

> **One router to run them all — every free AI model, auto-routed to the task it does best. Cost: ₹0.**

One trigger in, the **best free AI for the job** out. This repo gives you 4 importable
n8n workflows that route work across multiple free AI models automatically —
no paid APIs, no n8n Cloud, no credit card. **Total cost: ₹0.**

## How it works

```
[ Trigger: Webhook / Schedule ]
              │
              ▼
[ Classifier — free model ] ── detects intent: code | writing | research | quick-qa
              │
              ▼
[ Switch ] ──┬── Code Lane ──────► qwen3-coder:free
             ├── Writing Lane ───► llama-3.3-70b:free
             ├── Research Lane ──► deepseek-r1:free
             └── Quick QA Lane ──► gemma-3-27b:free
              │
              ▼
[ Action: API response / Gmail / Telegram / file ]
```

All AI calls go through **OpenRouter** (`openrouter.ai`) using its free `:free`
models. One free API key powers every model — no separate accounts per AI.

## The 4 workflows

| File | What it does | Trigger |
|---|---|---|
| `workflows/ai-smart-router.json` | Webhook receives `{task, text}` → classifies intent → routes to the best free model per lane → returns answer | Webhook `POST /ai-route` |
| `workflows/autonomous-morning-brief.json` | Writes your morning brief and emails it to you, every day at 7am | Schedule (cron `0 7 * * *`) |
| `workflows/lead-auto-responder.json` | New lead JSON in → AI drafts a personal reply → draft lands on your Telegram for one-tap approval | Webhook `POST /new-lead` |
| `workflows/content-pipeline.json` | Rotates through a topic list, generates a post draft, saves it as a markdown file — daily at 9am | Schedule (cron `0 9 * * *`) |

## Setup — 100% free (10 minutes)

### 1. Install n8n (free, self-hosted)

```bash
# Option A — npm (needs Node.js 18+)
npx n8n

# Option B — Docker
docker run -d --name n8n -p 5678:5678 \
  -e OPENROUTER_API_KEY="your-key-here" \
  -v n8n_data:/home/node/.n8n \
  n8nio/n8n
```

Open `http://localhost:5678` and create your (free, local) owner account.

### 2. Get a free OpenRouter key

1. Sign up at **openrouter.ai** (free tier, no card required).
2. Create an API key under Keys.
3. Pass it to n8n as the environment variable `OPENROUTER_API_KEY`
   (see the Docker command above, or export it before `npx n8n`).
4. The workflows reference it as `={{ $env.OPENROUTER_API_KEY }}` —
   the key never lives inside the workflow files.

> Free models change over time. Check what's currently free at
> `openrouter.ai/models` and swap any model id (they all end in `:free`).

### 3. Import the workflows

In n8n: **Workflows → ⋯ → Import from file** → pick a JSON from `workflows/`.
n8n will prompt you for any missing credentials (see per-workflow notes below).

## Per-workflow config notes

- **AI Smart Router** — no credentials needed beyond `OPENROUTER_API_KEY`.
  Test with: `curl -X POST http://localhost:5678/webhook/ai-route -H "Content-Type: application/json" -d '{"task":"code","text":"Write a Python quicksort"}'`
  (Use the production webhook URL once the workflow is active.)
- **Morning Brief** — add your free Gmail OAuth2 credential on the Gmail node
  (n8n prompts on import). Change the recipient/schedule to taste.
- **Lead Auto-Responder** — create a free bot with Telegram's `@BotFather`,
  add the Telegram credential in n8n, and set a `TELEGRAM_CHAT_ID` env var
  with your chat id. Nothing is sent to the lead automatically — you approve
  the draft first.
- **Content Pipeline** — drafts are written to `/home/node/drafts/` as
  markdown files (works in Docker and npm installs). Edit the topic list
  inside the **Pick Today's Topic** code node, or swap it for a Google
  Sheets node if you prefer.

## Cost: ₹0 — breakdown

| Piece | Cost |
|---|---|
| n8n (self-hosted) | Free & open source |
| OpenRouter `:free` models | Free (rate-limited) |
| Gmail / Telegram | Free |
| Everything in this repo | Free (MIT) |

## Honest limits

- **Free models have rate limits and daily caps.** If a lane starts failing,
  you've hit the cap — wait, or swap in another `:free` model id.
- **Free model ids change.** A model listed here may be renamed or retired;
  check `openrouter.ai/models` and update the id in the HTTP node.
- **The router doesn't invent workflows.** It automates the ones you design —
  you still decide the triggers and the actions.
- **`$env` access** requires self-hosted n8n (works out of the box with the
  install above; n8n Cloud blocks env access on some plans).
- **Leads are never auto-replied** in the template — the draft comes to you
  first. That's deliberate.

## License

MIT — use it, fork it, sell services built on it.

## ☕ Support my work
If this project was useful, you can support it with Bitcoin: `bc1q6q75k8zjxvw7w02lmdprpy6xx6qk4lzz2rmvay`
