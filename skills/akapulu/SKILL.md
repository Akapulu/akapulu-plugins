---
name: akapulu
description: >-
  Build on Akapulu Labs — talking-avatar video on top of a Pipecat voice
  pipeline, or a hosted link without a bot. Follow docs.akapulu.com/agent-setup.md.
metadata:
  author: akapulu
license: Apache-2.0
---

# Akapulu Labs

Akapulu adds a **talking avatar** on a real-time call. You build the voice stack; we add the face. Or use a **hosted link** for a quick call with no bot. Docs: https://docs.akapulu.com. Installer: https://docs.akapulu.com/agent-setup.md. Pricing (conversation minutes): https://akapulu.com/pricing/.

**Pipecat path.** Install public package [`pipecat-akapulu`](https://github.com/Akapulu/pipecat-akapulu) (`uv add` / `pip install`). `AkapuluAvatar` sits after assistant audio. You own the Daily room (browser + bot + avatar). Guide: https://docs.akapulu.com/guides/calls/your-bot. Example: https://github.com/Akapulu/akapulu-examples.

**Hosted link.** Public page we host. Set avatar, system prompt, This is AI → URL → Start Call. Guide: https://docs.akapulu.com/guides/calls/hosted-links.

**Account.** Sign up / sign in at https://akapulu.com/, finish in-app onboarding if new, then create a key at https://akapulu.com/api-keys/ → `AKAPULU_API_KEY`.
