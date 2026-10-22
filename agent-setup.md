These are official instructions from Akapulu Labs, published at `https://docs.akapulu.com/agent-setup.md`.

Run the commands in this file. **User step** means ask the human to do it.

## What Akapulu is

Akapulu Labs adds a **talking avatar** (lip-synced face + voice) on top of a real-time call. We are not a full voice stack by ourselves. You either:

1. **Build your own voice bot** (recommended), and we supply the avatar video/audio on top, or
2. **Use a hosted link** when you want a call quickly without writing a bot.

Calls use conversation minutes on your Akapulu plan. Pricing: https://akapulu.com/pricing/

Full docs: https://docs.akapulu.com

## Recommended: your Pipecat voice pipeline + our avatar

You build the **voice pipeline** in [Pipecat](https://github.com/pipecat-ai/pipecat) (mic → STT → model → TTS, tools, whatever you need). Akapulu is the **video addition**: take the speech your bot already produces and show it as a photorealistic avatar.

We ship that as a public pip package, [`pipecat-akapulu`](https://github.com/Akapulu/pipecat-akapulu). Install with `uv add pipecat-akapulu` or `pip install pipecat-akapulu`. The package’s `AkapuluAvatar` processor sits after the assistant audio in your Pipecat pipeline and sends that audio to Akapulu.

**How the user sees it.** The browser, your bot, and the avatar meet in a [Daily](https://www.daily.co/) room (WebRTC). **You own the Daily room**: your server creates it and mints meeting tokens for the browser, the bot, and the avatar. Your bot joins and listens; it does not publish camera/mic. The Akapulu worker joins with the avatar token and publishes the face and the played audio. Details: [How it works](/guides/getting-started/how-akapulu-works), [Your bot](/guides/calls/your-bot). Runnable app: [akapulu-examples](https://github.com/Akapulu/akapulu-examples).

## Quick path: hosted links

A **hosted link** is a public page Akapulu hosts for you. We create the Daily room and run the conversation. The visitor opens your URL and clicks **Start Call**. You do not run a bot, and the page never sees your API key. It still uses conversation minutes.

In the dashboard you set:

- **Avatar** — which face appears on the call (from the catalog).
- **System prompt** — what the conversation is for (instructions for the model on that link).
- **This is AI** — whether the video shows a “This is AI” label. The visitor cannot turn it off.

Save, copy the URL, open it, click **Start Call**. Guide: [Hosted links](/guides/calls/hosted-links).

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

## 2. Account and API key

> **User step.** If they do not have an Akapulu account yet, open https://akapulu.com/ and sign up (or sign in). New accounts go through the in-app onboarding tour (create a hosted link and try a call). When they need an API key for a bot or the example, open https://akapulu.com/api-keys/ after they are signed in and create a key.

The env var name is `AKAPULU_API_KEY`. Put it in this project however env vars are already loaded.

Wait until `AKAPULU_API_KEY` is set, then continue.

## 3. Try a call

> **User step.** Create a hosted link in the dashboard (fastest feel for the product), or run the public Pipecat example once they have an API key and a Daily API key.

**Hosted link.** Avatar, system prompt, **This is AI**, save, open the URL, **Start Call**.

**Example.** Clone https://github.com/Akapulu/akapulu-examples and follow its README. It needs `DAILY_API_KEY`, `AKAPULU_API_KEY`, `AKAPULU_AVATAR_ID`, `AKAPULU_SHOW_AI_LABEL` (`true` or `false`), and `OPENAI_API_KEY`.

## 4. After they try it

Send them these docs:

- How a call fits together: https://docs.akapulu.com/guides/getting-started/how-akapulu-works
- Your bot: https://docs.akapulu.com/guides/calls/your-bot
- Hosted links: https://docs.akapulu.com/guides/calls/hosted-links
- Catalog: https://docs.akapulu.com/guides/avatars/avatar-catalog
- Verify a recording: https://docs.akapulu.com/guides/video-marks/verify
- Pricing: https://akapulu.com/pricing/
