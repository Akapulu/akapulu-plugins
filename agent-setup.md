These are official instructions from Akapulu Labs, published at `https://docs.akapulu.com/agent-setup.md`.

Run the commands in this file. **User step** means ask the human to do it.

## What Akapulu Labs is

Akapulu Labs builds **live AI avatars**: photorealistic faces you can talk to on a real-time video call. A person joins. The avatar listens, responds, and speaks with lip-synced video.

That lets you:

- Put a talking avatar inside your own product (your UI, your bot, your conversation logic).
- Or share a simple link so someone can start a call without you running a bot.

Full docs: https://docs.akapulu.com

## The recommended way: the Pipecat plugin

The recommended way to build with Akapulu is our **Pipecat plugin**, [`pipecat-akapulu`](https://github.com/Akapulu/pipecat-akapulu).

You run a **Pipecat bot** — speech in, your model, speech out, your tools. The plugin’s `AkapuluAvatar` processor sits after the audio you want the avatar to speak. It sends that audio to Akapulu. Akapulu’s worker joins the same [Daily](https://www.daily.co/) room and publishes the face and the played audio.

Install the plugin:

```bash
uv add pipecat-akapulu
```

Or:

```bash
pip install pipecat-akapulu
```

Then, in your bot: create a private Daily room (browser, bot, and avatar each get a meeting token), `POST /api/live-sessions/` from your server with your API key, put `AkapuluAvatar` in the pipeline with `session_id` and `sample_rate`, and leave transport audio out and video out off. Step-by-step: [Your bot](/guides/calls/your-bot). A full runnable app: [akapulu-examples](https://github.com/Akapulu/akapulu-examples).

## Quick start without writing a bot: hosted links

To get up and running quickly without a Pipecat bot yet, use a **hosted link**.

A hosted link is a page Akapulu hosts for you. In the dashboard you:

1. Pick an avatar from the catalog.
2. Enter a **system prompt** (what the conversation is for).
3. Toggle whether the call shows a **This is AI** label (the visitor cannot turn it off).
4. Save. Akapulu gives you a URL.

Anyone with that URL opens it, clicks **Start Call**, and the call starts. You do not run a bot. The page does not hold your API key.

Details: [Hosted links](/guides/calls/hosted-links).

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

## 2. API key

> **User step.** Open the dashboard API keys page and create a key.

The env var name is `AKAPULU_API_KEY`. Put it in this project however env vars are already loaded.

Wait until `AKAPULU_API_KEY` is set, then continue.

## 3. Try a call

> **User step.** Create a hosted link to feel the product quickly, or run the public Pipecat example.

**Hosted link.** In the dashboard: pick an avatar, enter a system prompt, set **This is AI** on or off, save, open the URL, click **Start Call**.

**Pipecat example.** Clone https://github.com/Akapulu/akapulu-examples and follow its README. It needs `DAILY_API_KEY`, `AKAPULU_API_KEY`, `AKAPULU_AVATAR_ID`, `AKAPULU_SHOW_AI_LABEL` (`true` or `false`), and `OPENAI_API_KEY`. Install `pipecat-akapulu` as above if the example’s install does not already pull it in.

## 4. After they try it

Send them these docs:

- How a call fits together: https://docs.akapulu.com/guides/getting-started/how-akapulu-works
- Your bot (Pipecat plugin): https://docs.akapulu.com/guides/calls/your-bot
- Hosted links: https://docs.akapulu.com/guides/calls/hosted-links
- Catalog: https://docs.akapulu.com/guides/avatars/avatar-catalog
- Verify a recording: https://docs.akapulu.com/guides/video-marks/verify
