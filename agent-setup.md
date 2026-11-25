These are official instructions from Akapulu Labs, published at `https://docs.akapulu.com/agent-setup.md`.

Run the commands in this file. **User step** means ask the human to do it. Do not skip the asks. Do not assume their answers.

## What Akapulu Labs is

Akapulu Labs builds **live AI avatars** — a talking face (lip-synced video + voice) on a real-time call.

Two product shapes:

- A **hosted link** — a public page Akapulu Labs hosts. Open the URL, click **Start Call**. No bot to run; the page never sees your API key.
- Your **own avatar chat** — you run a **voice pipeline** (speech-to-speech, or STT → model → TTS, or anything that produces assistant audio). Akapulu Labs adds the avatar video on top in a WebRTC room you create (Daily with Pipecat, or LiveKit with LiveKit Agents).

Calls use conversation minutes. Pricing: https://akapulu.com/pricing/

Full docs: https://docs.akapulu.com

## 1. Install skills and docs MCP

### Claude Code

```bash
npx -y skills add Akapulu/akapulu-plugins --skill '*' --yes --global
```

```bash
claude mcp add --transport http akapulu-docs -s user https://docs.akapulu.com/mcp
```

> **User step.** Reload skills if they do not appear. Confirm `akapulu-docs` is connected.

### Codex

```bash
npx -y skills add Akapulu/akapulu-plugins --skill '*' --yes --global
```

```bash
codex mcp add akapulu-docs --url https://docs.akapulu.com/mcp
```

> **User step.** Reload if prompted. Confirm the MCP is connected.

### Cursor and other agents

```bash
npx -y skills add Akapulu/akapulu-plugins --skill '*' --yes --global
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

## 2. Do they have an account?

> **User step.** Ask: do you already have an Akapulu Labs account?

- **No.** Ask them to open https://akapulu.com/, sign up, then come back here when they are signed in.
- **Yes.** Continue.

Do not continue past this section until they have an account (or confirm they are signed in).

## 3. Quick talk, or build their own chat?

> **User step.** Ask which they want:

1. **A super-quick link** — talk to an avatar soon, without building a bot.
2. **Build their own custom avatar chat** — a Pipecat (Daily) or LiveKit Agents voice pipeline with the Akapulu Labs avatar in their app.

Wait for the answer. Then follow **only** that branch.

---

## Path 1: Super-quick link

> **User step.** Ask how they want that link made:

1. **You create it with their API key** — you call the API and hand them a URL to paste in a browser.
2. **They use the Akapulu Labs UI** — in-app onboarding / Hosted links in the dashboard.

### Path 1a: You create the hosted link

A **hosted link** is a public page Akapulu Labs hosts. You create the row; they open `url` and click **Start Call**. Docs: [Hosted links](/guides/calls/hosted-links), [Create a hosted link](/api-reference/hosted-links/create).

**Defaults for this path (do not ask):**

- Avatar: **Clara** — `avatar_id` `1f777f64-3758-4a7d-9cbc-c64ae654f7d1`
- **This is AI** (`show_ai_label`): `false`

> **User step.** Guide them to create an API key at https://akapulu.com/api-keys/. Then instruct them to activate `AKAPULU_API_KEY` in **this** environment (however this project already loads env vars). Do **not** ask them to paste the key into chat.

Wait until `AKAPULU_API_KEY` is available in this environment.

> **User step.** Ask what kind of chat they want — what the avatar should talk about, tone, goal. Then **you** write a good `system_prompt` from their answer.

When you write that prompt:

- The model’s replies are spoken by TTS on the call.
- Do not tell the model to output markdown, lists of symbols, code, URLs spelled letter-by-letter, or anything awkward to speak aloud.
- Prefer short, conversational sentences.

Then `POST /api/hosted-links/` with Bearer auth and a JSON body that is exactly:

```json
{
  "label": "Clara",
  "avatar_id": "1f777f64-3758-4a7d-9cbc-c64ae654f7d1",
  "system_prompt": "<your TTS-friendly prompt>",
  "show_ai_label": false
}
```

Give them the `url` from the response.

> **User step.** Open that `url` in a browser and click **Start Call**.

### Path 1b: Onboarding in the Akapulu Labs UI

No API key for this path.

> **User step.** Open https://akapulu.com/ signed in. New accounts get the hosted-link tour. Create a hosted link (avatar, system prompt, **This is AI**), save, open the URL, click **Start Call**.

If the tour is already finished, they can still create a link under Hosted links. Guide: [Hosted links](/guides/calls/hosted-links).

---

## Path 2: Build their own custom avatar chat

> **User step.** Ask which stack they use:

1. **Pipecat on Daily** — [`pipecat-akapulu`](https://github.com/Akapulu/pipecat-akapulu), `AkapuluAvatar`, Daily room. Guide: [Pipecat](/guides/calls/pipecat).
2. **LiveKit Agents** — [`livekit-akapulu`](https://github.com/Akapulu/livekit-akapulu), `AvatarSession`, LiveKit room. Guide: [LiveKit](/guides/calls/livekit).

Wait for the answer. Then follow **only** that branch.

### Names (both stacks)

- **Voice bot** — their voice pipeline. It hears the visitor and produces **assistant audio**. It does not publish the face.
- **Akapulu Labs worker** — Akapulu Labs’ avatar process. It joins the room and publishes the lip-synced face and the played audio.
- **Plugin** — on Pipecat, `AkapuluAvatar`; on LiveKit Agents, `AvatarSession`. Runs inside the voice bot / agent session and forwards assistant audio to the worker.

### Path 2a: Pipecat / Daily

Before wiring code, situate **Daily**, the **voice bot**, the **Akapulu Labs worker**, and **`AkapuluAvatar`**.

[Daily](https://www.daily.co/) is the WebRTC room. On this path **they** create that room. `create_akapulu_call` reads `DAILY_API_KEY` and `AKAPULU_API_KEY`, creates the private Daily room, mints the three meeting tokens, and `POST /api/live-sessions/`. Put **`AkapuluAvatar`** after assistant audio with `session_id`. Leave voice-bot audio out and video out off. When the participant named **Avatar** joins, unsubscribe the voice bot from that microphone. The browser stays subscribed. Code: [Pipecat](/guides/calls/pipecat).

Install: `uv add pipecat-akapulu==0.1.22` or `pip install pipecat-akapulu==0.1.22`.

> **User step.** Ask: are you okay using the public example? Behind the scenes it runs **Deepgram Flux**, **OpenAI** (`gpt-5.4-mini`), and **Cartesia Sonic 3.6**.

#### Path 2a-i: Yes — use the example

The public example ([akapulu-examples](https://github.com/Akapulu/akapulu-examples)) is a local server, a browser page, and a **voice bot**. It creates the Daily room, calls live-sessions, and runs **`AkapuluAvatar`** inside that voice bot.

> **User step.**

- Create an Akapulu API key at https://akapulu.com/settings/api-keys/ if they do not have one. Instruct them to activate `AKAPULU_API_KEY` in this environment (do not paste the key into chat).
- They also need a Daily API key as `DAILY_API_KEY`.
- They also need `OPENAI_API_KEY`, `DEEPGRAM_API_KEY`, and `CARTESIA_API_KEY`. If one of those is not already in this environment, instruct them to activate it here.

Wait until those env vars are set. Clone https://github.com/Akapulu/akapulu-examples and follow its README. Run the server, open the local page, start a call.

#### Path 2a-ii: No — bring their own Pipecat pipeline

Still clone https://github.com/Akapulu/akapulu-examples as a **reference**. Then guide them through their own Pipecat **voice bot** with **`AkapuluAvatar`**.

> **User step.** Activate `AKAPULU_API_KEY` and `DAILY_API_KEY` in this environment. Do not paste secrets into chat.

Three participants in the Daily room they create: browser, voice bot (with `AkapuluAvatar`), Akapulu Labs worker. Lifecycle: `create_akapulu_call` (or manual `POST /api/live-sessions/`), start the voice bot with `session_id`, join the browser. Keep `AKAPULU_API_KEY` on the server.

Details: [How it works](/guides/getting-started/how-akapulu-works), [Pipecat](/guides/calls/pipecat).

### Path 2b: LiveKit Agents

Before wiring code, situate **LiveKit**, the **voice agent**, the **Akapulu Labs worker**, and **`AvatarSession`**.

[LiveKit](https://livekit.io/) is the WebRTC room. On this path **they** create that room. `create_akapulu_livekit_call` reads `LIVEKIT_URL`, `LIVEKIT_API_KEY`, `LIVEKIT_API_SECRET`, and `AKAPULU_API_KEY`, creates the room, mints browser and agent tokens, mints the Avatar JWT, and `POST /api/live-sessions/` with `transport=livekit`. Or call `AvatarSession.start` when the agent is already in the room so the plugin mints and POSTs. Await `wait_until_call_ready`. Mute `session.input` until then. Loading status is on LiveKit data topic `akapulu.avatar.status`. Code: [LiveKit](/guides/calls/livekit).

Install: `uv add livekit-akapulu==0.1.1` or `pip install livekit-akapulu==0.1.1`. Requires `livekit-agents` 1.8 or newer.

> **User step.** Activate `AKAPULU_API_KEY`, `LIVEKIT_URL`, `LIVEKIT_API_KEY`, and `LIVEKIT_API_SECRET` in this environment. Do not paste secrets into chat. Also set `AKAPULU_AVATAR_ID` when `AvatarSession.start` opens the session itself.

Three participants in the LiveKit room: browser, voice agent (with `AvatarSession`), Akapulu Labs worker (`Avatar`). Keep `AKAPULU_API_KEY` on the server.

Details: [How it works](/guides/getting-started/how-akapulu-works), [LiveKit](/guides/calls/livekit).

## 4. After they try it

Send them these docs:

- How a call fits together: https://docs.akapulu.com/guides/getting-started/how-akapulu-works
- Pipecat: https://docs.akapulu.com/guides/calls/pipecat
- LiveKit: https://docs.akapulu.com/guides/calls/livekit
- Hosted links: https://docs.akapulu.com/guides/calls/hosted-links
- Catalog: https://docs.akapulu.com/guides/avatars/avatar-catalog
- Verify a recording: https://docs.akapulu.com/guides/video-marks/verify
- Pricing: https://akapulu.com/pricing/
