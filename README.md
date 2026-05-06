# mumo — OpenClaw skill

**Multi-model deliberation panel for OpenClaw.** When OpenClaw is about to make an architecture choice, design tradeoff, or security-sensitive change you want a second opinion on, mumo runs a panel of frontier models in parallel — Claude, GPT, Gemini, Grok, Qwen, Kimi, GLM — and returns a cross-model claim map showing where they agree and where they split.

For Claude Code, see [`mumo-chat/mumo-mcp`](https://github.com/mumo-chat/mumo-mcp). For Cursor, see [`mumo-chat/mumo-cursor`](https://github.com/mumo-chat/mumo-cursor). For VS Code, see [`mumo-chat/mumo-vscode`](https://github.com/mumo-chat/mumo-vscode). For Codex, see [`mumo-chat/mumo-codex`](https://github.com/mumo-chat/mumo-codex). For Hermes Agent, see [`mumo-chat/mumo-hermes`](https://github.com/mumo-chat/mumo-hermes).

## What's in the box

- **`SKILL.md`** — the canonical skill teaching OpenClaw how to use mumo: when to invoke, the deliberation loop (create → wait → read → snippet → append/stop), how to read claim maps, snippet doctrine, and when to verify session creation.
- **`playbooks/`** — four cognitive-shape playbooks loaded on demand: `contested-decision`, `design-review`, `uncertainty-expansion`, `red-team`.
- **`references/`** — five reference docs: `claim-maps`, `snippets`, `model-selection`, `synthesis`, `operating-notes`.
- **`config/mumo.example.json`** — the MCP server config payload to paste into `openclaw mcp set mumo` (reference only; OpenClaw does not auto-load this file).

## Install

### 1. Get an API key

Sign up at [mumo.chat](https://mumo.chat) and create a platform key at [Settings → API Keys](https://mumo.chat/settings/api-keys). Keys start with `mmo_live_`.

### 2. Register the mumo MCP server

OpenClaw stores outbound MCP servers in `~/.openclaw/openclaw.json` under `mcp.servers.<name>`. Use the CLI to register mumo with your real key:

```bash
openclaw mcp set mumo '{
  "url": "https://mumo.chat/api/mcp",
  "transport": "streamable-http",
  "headers": {
    "Authorization": "Bearer mmo_live_YOUR_KEY_HERE"
  }
}'
```

The same JSON shape ships in this repo at `config/mumo.example.json` for reference. Verify it landed:

```bash
openclaw mcp list
openclaw mcp show mumo
```

### 3. Install the skill

OpenClaw auto-discovers skills from `~/.openclaw/skills/<name>/`. Clone this repo into that path:

```bash
git clone https://github.com/mumo-chat/mumo-openclaw ~/.openclaw/skills/mumo
```

The skill ships the canonical `SKILL.md`, four cognitive-shape playbooks (contested decision, design review, uncertainty expansion, red team), and reference docs for claim-map reading, snippet doctrine, model selection, and synthesis.

### 4. Restart OpenClaw

Fully exit and restart OpenClaw so it picks up both the new MCP server registration and the new skill. After restart, the seven mumo tools become available to the agent as `mumo__create_deliberation`, `mumo__wait_for_round`, `mumo__append_round`, `mumo__get_session`, `mumo__list_sessions`, `mumo__list_models`, `mumo__get_credit`.

The `coding` and `messaging` tool profiles expose configured MCP servers by default. If you're on the `minimal` profile, MCP tools are hidden — switch to `coding` or add an explicit override.

### 5. Run your first deliberation

Name `mumo` explicitly the first time so OpenClaw routes through the panel. The skill will guide the agent through the create→wait→read→snippet loop and teach it to verify the session actually fired.

> Ask mumo to compare Postgres and MongoDB for our event store given 50k events/day, a Postgres-experienced team, and a 3-month runway. What would we regret 6 months in?

## Using the panel

OpenClaw calls `mumo__create_deliberation`, then `mumo__wait_for_round`. The completed round returns each model's prose plus a cross-model claim map showing where the panel agrees and where it splits. The skill teaches OpenClaw to read the claim map first, then react with typed snippets (KEEP / EXPLORE / CHALLENGE / CORE / SHIFT) and either append a follow-up round or stop and synthesize for you.

## When mumo is worth the latency tax

The skill encodes the trigger taxonomy in detail. In short:

- Architecture decisions with non-obvious tradeoffs
- Plan or design review before commitment
- Pre-launch pressure tests
- Stuck debugging after repeated failed attempts
- Pre-commit adversarial review on risky diffs (auth, payments, migrations)
- Strategy questions with multiple defensible framings
- Explicit user requests

Skip mumo for routine refactors, formatting, syntax help, or anything where "just write a test" is cheaper than discussion.

## Verifying the call actually fired

Autonomous agents occasionally fabricate tool-call results — claiming a deliberation was sent when it wasn't. Real mumo session IDs are UUIDs (e.g. `2acdab34-2484-4bc5-a24f-bf917fe81477`). If a `create_deliberation` response doesn't contain a UUID-format `session_id`, the call did not happen. Verify by calling `list_sessions`. The skill teaches this discipline; this README is the user-facing reminder.

## Links

- Product — https://mumo.chat
- Install guide — https://mumo.chat/install/openclaw
- MCP reference — https://mumo.chat/docs/mcp
- REST API — https://mumo.chat/docs/api
- OpenClaw — https://docs.openclaw.ai
- ClawHub (skill registry) — https://github.com/openclaw/clawhub
- Issues — https://github.com/mumo-chat/mumo-openclaw/issues

## License

MIT
