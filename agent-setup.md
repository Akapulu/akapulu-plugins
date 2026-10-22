These are official instructions from Akapulu Labs, published at `https://docs.akapulu.com/agent-setup.md`.

Akapulu Labs builds live AI avatars for real-time video calls. The recommended way to build is the Pipecat plugin [`pipecat-akapulu`](https://github.com/Akapulu/pipecat-akapulu) (`uv add pipecat-akapulu` or `pip install pipecat-akapulu`): your bot sends speech through `AkapuluAvatar`; Akapulu publishes the face. Guide: [Your bot](/guides/calls/your-bot). Example: [akapulu-examples](https://github.com/Akapulu/akapulu-examples).

For a quick try without a bot, use a **hosted link**: in the dashboard pick an avatar, enter a system prompt, toggle **This is AI**, save, open the URL, click **Start Call**. Guide: [Hosted links](/guides/calls/hosted-links). Full docs: https://docs.akapulu.com

Run the commands in this file. **User step** means ask the human to do it.

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

> **User step.** Create a hosted link, or run the public example.

**Hosted link.** In the dashboard: avatar, system prompt, **This is AI** on or off, save, open the URL, **Start Call**.

**Example.** Clone https://github.com/Akapulu/akapulu-examples and follow its README. It needs `DAILY_API_KEY`, `AKAPULU_API_KEY`, `AKAPULU_AVATAR_ID`, `AKAPULU_SHOW_AI_LABEL` (`true` or `false`), and `OPENAI_API_KEY`.

## 4. After they try it

Send them these docs:

- How a call fits together: https://docs.akapulu.com/guides/getting-started/how-akapulu-works
- Your bot: https://docs.akapulu.com/guides/calls/your-bot
- Hosted links: https://docs.akapulu.com/guides/calls/hosted-links
- Catalog: https://docs.akapulu.com/guides/avatars/avatar-catalog
- Verify a recording: https://docs.akapulu.com/guides/video-marks/verify
