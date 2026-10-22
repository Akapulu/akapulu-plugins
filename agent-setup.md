These are official instructions from Akapulu Labs, published at `https://docs.akapulu.com/agent-setup.md`.

Run the commands in this file. **User step** means ask the human to do it. Do not skip the asks. Do not assume their answers.

## What Akapulu Labs is

Akapulu Labs builds **live AI avatars** — a talking face (lip-synced video + voice) on a real-time call.

Two product shapes:

- A **hosted link** — a public page Akapulu Labs hosts. Open the URL, click **Start Call**. No bot to run; the page never sees your API key.
- Your **own avatar chat** — you run a **voice pipeline** (speech-to-speech, or STT → model → TTS, or anything that produces assistant audio). Akapulu Labs adds the avatar video on top in a Daily room you create and own.

Calls use conversation minutes. Pricing: https://akapulu.com/pricing/

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

## 2. Do they have an account?

> **User step.** Ask: do you already have an Akapulu Labs account?

- **No.** Ask them to open https://akapulu.com/, sign up, then come back here when they are signed in.
- **Yes.** Continue.

Do not continue past this section until they have an account (or confirm they are signed in).

## 3. Quick talk, or build their own chat?

> **User step.** Ask which they want:

1. **A super-quick link** — talk to an avatar soon, without building a bot.
2. **Build their own custom avatar chat** — a Pipecat voice pipeline with the Akapulu Labs avatar in their app.

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

They run a **voice pipeline** — speech-to-speech, STT → model → TTS, or any other stack that produces assistant audio. Akapulu Labs is the **video addition**: that assistant audio becomes a photorealistic talking face.

Install [`pipecat-akapulu`](https://github.com/Akapulu/pipecat-akapulu) with `uv add pipecat-akapulu` or `pip install pipecat-akapulu` when you wire a custom bot. The public example already depends on it.

### Path 2 ask: OpenAI Live, or their own voice bot?

> **User step.** Ask: are you okay using **OpenAI Live** (Realtime) as the voice-to-voice model behind the scenes in the public example?

#### Path 2a: Yes — use the example (OpenAI Live)

The example is a local server, a browser page, and a bot: [akapulu-examples](https://github.com/Akapulu/akapulu-examples).

> **User step.**

- Create an Akapulu API key at https://akapulu.com/api-keys/ if they do not have one. Instruct them to activate `AKAPULU_API_KEY` in this environment (do not paste the key into chat).
- They also need a Daily API key as `DAILY_API_KEY`.
- If `OPENAI_API_KEY` is not already in this environment, instruct them to activate it here.

Wait until those env vars are set.

Then clone https://github.com/Akapulu/akapulu-examples and follow its README. For the example avatar, use Clara:

- `AKAPULU_AVATAR_ID=1f777f64-3758-4a7d-9cbc-c64ae654f7d1`
- `AKAPULU_SHOW_AI_LABEL=false` (unless they ask otherwise)

Run the server, open the local page, start a call. That is their starter app.

#### Path 2b: No — bring their own voice pipeline

Still clone https://github.com/Akapulu/akapulu-examples as a **reference** (room + tokens + live session + page). Then guide them through their own Pipecat bot with `AkapuluAvatar`.

> **User step.** Activate `AKAPULU_API_KEY` and `DAILY_API_KEY` in this environment the same way as above. Do not paste secrets into chat.

##### Call architecture (explain this slowly, with bullets)

A live Akapulu call is a private [Daily](https://www.daily.co/) room with **three participants**:

- **Browser** — the person on the call. Joins with a browser meeting token you mint.
- **Their bot** — runs their voice pipeline. Joins with a bot meeting token. **Listens** to the browser. Does **not** publish camera or mic.
- **Avatar** — the Akapulu Labs worker. Joins with an avatar meeting token. Publishes the face and the played audio.

They **own** the Daily room. Akapulu Labs does not create that room for the plugin path.

##### Lifecycle (in order)

1. **Their server creates a Daily room** and mints three meeting tokens (browser, bot, avatar).
2. **Their server calls** `POST /api/live-sessions/` with Bearer `AKAPULU_API_KEY` and JSON:
   - `avatar_id` (use Clara `1f777f64-3758-4a7d-9cbc-c64ae654f7d1` unless they pick another from https://akapulu.com/catalog)
   - `show_ai_label` (boolean)
   - `room_url` (the Daily room they just created)
   - `avatar_token` (the avatar meeting token)
3. The response is `session_id`. That starts the avatar worker; it does not wait for the first frame.
4. **Their bot starts**, joins the room with the bot token, and passes `session_id` into `AkapuluAvatar` (after the assistant audio in the pipeline).
5. **The browser joins** with the browser token (usually from their page / server).
6. Loading status arrives on the bot as a server message with `type` `status`. Do not poll Django for it.
7. When the bot produces assistant audio, `AkapuluAvatar` sends that audio to Akapulu Labs; the avatar publishes lip-synced video + that audio in the room.

Keep `AKAPULU_API_KEY` on the server. The browser never gets it.

Details: [How it works](/guides/getting-started/how-akapulu-works), [Your bot](/guides/calls/your-bot).

## 4. After they try it

Send them these docs:

- How a call fits together: https://docs.akapulu.com/guides/getting-started/how-akapulu-works
- Your bot: https://docs.akapulu.com/guides/calls/your-bot
- Hosted links: https://docs.akapulu.com/guides/calls/hosted-links
- Catalog: https://docs.akapulu.com/guides/avatars/avatar-catalog
- Verify a recording: https://docs.akapulu.com/guides/video-marks/verify
- Pricing: https://akapulu.com/pricing/
