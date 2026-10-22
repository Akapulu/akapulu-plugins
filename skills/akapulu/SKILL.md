---
name: akapulu
description: >-
  Build on Akapulu Labs — live talking-avatar video calls. Use when installing
  pipecat-akapulu, wiring AkapuluAvatar in a Pipecat bot, creating a hosted
  link, or following docs.akapulu.com/agent-setup.md.
metadata:
  author: akapulu
license: Apache-2.0
---

# Akapulu Labs

Akapulu Labs builds **live AI avatars**: photorealistic faces on real-time video calls. Docs: https://docs.akapulu.com. Docs MCP: `akapulu-docs` (`https://docs.akapulu.com/mcp`). REST: `https://akapulu.com/api/` with Bearer `AKAPULU_API_KEY`.

Installer for coding agents: https://docs.akapulu.com/agent-setup.md

## Recommended path: Pipecat plugin

Install [`pipecat-akapulu`](https://github.com/Akapulu/pipecat-akapulu):

```bash
uv add pipecat-akapulu
# or: pip install pipecat-akapulu
```

You run a Pipecat bot. `AkapuluAvatar` sits after the audio you want the avatar to speak. Create a private Daily room (browser, bot, avatar tokens), `POST /api/live-sessions/` from the server, leave transport audio out and video out off. Guide: https://docs.akapulu.com/guides/calls/your-bot. Example app: https://github.com/Akapulu/akapulu-examples.

## Quick path: hosted links

No bot required. In the dashboard: pick an avatar, enter a system prompt, toggle **This is AI**, save, open the URL, click **Start Call**. Guide: https://docs.akapulu.com/guides/calls/hosted-links.

## First thing to build

1. Follow https://docs.akapulu.com/agent-setup.md (skills, docs MCP, API key).
2. API key: create at https://akapulu.com/api-keys. Env var: `AKAPULU_API_KEY`.
3. Either create a hosted link so they can try a call immediately, or clone https://github.com/Akapulu/akapulu-examples and run it.

## After they try a call

Send docs:

- How it works: https://docs.akapulu.com/guides/getting-started/how-akapulu-works
- Your bot: https://docs.akapulu.com/guides/calls/your-bot
- Hosted links: https://docs.akapulu.com/guides/calls/hosted-links
- Catalog: https://docs.akapulu.com/guides/avatars/avatar-catalog
- Verify a recording: https://docs.akapulu.com/guides/video-marks/verify
