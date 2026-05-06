# Changelog

## 0.1.1 — 2026-05-06

License switched from standard MIT to MIT-0 (MIT No Attribution) to match ClawHub's published-skill license requirement. MIT-0 is strictly more permissive than MIT — drops the "must include copyright notice" clause; everything else identical. Doesn't affect runtime, install, or distribution. The other 5 mumo platform repos remain on standard MIT.

## 0.1.0 — 2026-05-06

Initial release. OpenClaw skill + MCP config for mumo's multi-model deliberation server.

- `SKILL.md` — kernel teaching the deliberation loop, snippet doctrine, claim-map reading, when-to-invoke triggers, verification discipline (real session IDs are UUIDs; verify with `list_sessions` if uncertain), and a **"Framing prompts for the panel"** section that tells the agent NOT to pass user role-instructions verbatim — translate to first-person moderator context so panel models advise the agent rather than roleplaying it. AgentSkills-compatible frontmatter (single-line per OpenClaw's parser; `metadata` as single-line JSON).
- `config/mumo.example.json` — reference payload for `openclaw mcp set mumo` (named `.example.json` to telegraph "OpenClaw does not auto-load this file; it's the JSON shape to paste into the CLI"). Streamable HTTP transport against `https://mumo.chat/api/mcp` with literal Bearer auth in the `headers` map (OpenClaw's MCP client doesn't support env-var pointers for HTTP headers; users paste the `mmo_live_*` key directly into the JSON).
- `playbooks/` — four cognitive-shape playbooks: `contested-decision`, `design-review`, `uncertainty-expansion`, `red-team`.
- `references/` — five reference docs: `claim-maps`, `snippets`, `model-selection`, `synthesis`, `operating-notes`. Plural directory naming matches mumo-codex / Codex convention; OpenClaw's auto-loader doesn't care about the directory name, only `SKILL.md`.
- README install steps: `openclaw mcp set mumo` for the server, `git clone … ~/.openclaw/skills/mumo` for the skill, restart OpenClaw, run the first deliberation.

Tool naming note: OpenClaw exposes MCP tools to the agent as `<server>__<tool>` (double underscore). So mumo's seven tools surface as `mumo__create_deliberation`, `mumo__wait_for_round`, etc. The SKILL.md uses canonical (unprefixed) tool names since the prefix is OpenClaw-specific; the agent maps them automatically.

Derived from the v0.1.x kernel that originated in mumo-mcp / mumo-cursor and was adapted for Hermes in mumo-hermes. OpenClaw's surface is closest to Hermes (skill + manual MCP server config, no plugin manifest layer) — same paradigm, different paths and command shapes.
