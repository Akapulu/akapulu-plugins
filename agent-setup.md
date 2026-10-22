These are official instructions from Akapulu Labs, published at `https://docs.akapulu.com/agent-setup.md`.

Run the commands in this file. **User step** means ask the human to do it.

## What Akapulu Labs is

Akapulu Labs builds **live AI avatars** — a talking face (lip-synced video + voice) on a real-time call. You can try that as:

- A **hosted link** — a public page Akapulu Labs hosts. Open the URL, click **Start Call**. No bot to run; the page never sees your API key.
- Your **Pipecat bot** — you build the voice pipeline; Akapulu Labs adds the avatar video on top in a Daily room you create and own.

Calls use conversation minutes on your Akapulu Labs plan. Pricing: https://akapulu.com/pricing/

Full docs: https://docs.akapulu.com

## 1. Install skills and docs MCP

### Claude Code

```bash
npx -y skills add william-akapulu/akapulu-plugins --skill '*' --yes --global
```

```bash
claude mcp add --transport http akapulu-docs -s user https://docs.akapulu.com/mcp
```

> **User step.** Reload skills if they do not appear. Confirm `akapulu-docs` is connected.

### Codex

```bash
npx -y skills add william-akapulu/akapulu-plugins --skill '*' --yes --global
```

```bash
codex mcp add akapulu-docs --url https://docs.akapulu.com/mcp
```

> **User step.** Reload if prompted. Confirm the MCP is connected.

### Cursor and other agents

```bash
npx -y skills add william-akapulu/akapulu-plugins --skill '*' --yes --global
```

Add the docs MCP to `~/.cursor/mcp.json` (or this project's `.cursor/mcp.json`). Merge into `mcpServers` if the file already exists:

```json
{
  "mcpServers": {
    "akapulu-docs": {
      "url": "https://docs.akapulu.com/mcp"
    }
  }
}
```

> **User step.** Reload the agent. In Cursor, enable `akapulu-docs` under Settings → MCP.

## 2. Account

> **User step.** If they do not have an Akapulu Labs account yet, open https://akapulu.com/ and sign up (or sign in).

They need an account for every path below. An API key is only required for **Option A** and **Option C**.

## 3. Ask which path they want

> **User step.** Ask them to pick **one** (do not assume):

1. **Agent creates a hosted link** — they create an API key; you call the API and give them a URL; they paste it in a browser and click **Start Call**. Fastest try from chat.
2. **Browser onboarding** — they walk the in-app tour (create a hosted link and try a call in the dashboard). No API key for that tour.
3. **Pipecat bot** — they run their own voice bot with the Akapulu Labs avatar (or the public example). Needs an API key and a Daily API key.

Wait for their answer. Then follow **only** that option.

## Option A: Agent creates a hosted link

A **hosted link** is a public page Akapulu Labs hosts. You create the row with their API key; they open the `url` and click **Start Call**. You do not run a bot. Docs: [Hosted links](/guides/calls/hosted-links), [Create a hosted link](/api-reference/hosted-links/create).

> **User step.** Open https://akapulu.com/api-keys/ and create a key. Give you the value (or put it where this project already loads env vars). The env var name is `AKAPULU_API_KEY`.

Wait until `AKAPULU_API_KEY` is set.

> **User step.** Open https://akapulu.com/catalog and pick a public avatar. Send you its UUID (`avatar_id`). Catalog guide: https://docs.akapulu.com/guides/avatars/avatar-catalog

Then `POST /api/hosted-links/` with Bearer auth and a JSON body that is exactly:

```json
{
  "label": "Desk",
  "avatar_id": "<avatar uuid>",
  "system_prompt": "<what the conversation is for>",
  "show_ai_label": true
}
```

`show_ai_label` must be a boolean. Agree a short `label` and `system_prompt` with them if they did not specify. The response includes `url` and `token`.

> **User step.** Open that `url` in a browser and click **Start Call**.

## Option B: Browser onboarding

They try the product in the dashboard. No API key for this path.

> **User step.** Open https://akapulu.com/ signed in. New accounts get the hosted-link tour. Create a hosted link (avatar, system prompt, **This is AI**), save, open the URL, click **Start Call**.

If the tour is already finished, they can still create a link under Hosted links in the app. Guide: [Hosted links](/guides/calls/hosted-links).

## Option C: Pipecat bot

They build the **voice pipeline** in [Pipecat](https://github.com/pipecat-ai/pipecat) (mic → STT → model → TTS, tools, whatever they need). Akapulu Labs is the **video addition**: take the speech the bot already produces and show it as a photorealistic avatar.

Install [`pipecat-akapulu`](https://github.com/Akapulu/pipecat-akapulu) with `uv add pipecat-akapulu` or `pip install pipecat-akapulu`. Its `AkapuluAvatar` processor sits after assistant audio and sends that speech to Akapulu Labs.

**How the call fits.** The browser, their bot, and the avatar meet in a [Daily](https://www.daily.co/) room. **They own the Daily room**: their server creates it and mints meeting tokens for the browser, the bot, and the avatar. The bot joins and listens; it does not publish camera/mic. The Akapulu Labs worker joins with the avatar token and publishes the face and the played audio. Details: [How it works](/guides/getting-started/how-akapulu-works), [Your bot](/guides/calls/your-bot).

> **User step.** Create an API key at https://akapulu.com/api-keys/ if they do not have one. Put it in `AKAPULU_API_KEY`. They also need a Daily API key and (for the example) an OpenAI key.

Wait until `AKAPULU_API_KEY` is set, then continue.

> **User step.** Clone https://github.com/Akapulu/akapulu-examples and follow its README, or wire `AkapuluAvatar` into their existing Pipecat bot. The example needs `DAILY_API_KEY`, `AKAPULU_API_KEY`, `AKAPULU_AVATAR_ID`, `AKAPULU_SHOW_AI_LABEL` (`true` or `false`), and `OPENAI_API_KEY`. Pick `AKAPULU_AVATAR_ID` from https://akapulu.com/catalog.

## 4. After they try it

Send them these docs:

- How a call fits together: https://docs.akapulu.com/guides/getting-started/how-akapulu-works
- Your bot: https://docs.akapulu.com/guides/calls/your-bot
- Hosted links: https://docs.akapulu.com/guides/calls/hosted-links
- Catalog: https://docs.akapulu.com/guides/avatars/avatar-catalog
- Verify a recording: https://docs.akapulu.com/guides/video-marks/verify
- Pricing: https://akapulu.com/pricing/
