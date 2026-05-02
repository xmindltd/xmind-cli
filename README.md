# xmind-cli

Xmind for AI agents — create and edit `.xmind` mind maps from Claude Code, Cursor, Codex, Gemini CLI and more.

Works on local files and live cloud documents at [xmind.app](https://xmind.app).

## Install

```bash
# 1. Install the CLI
npm install -g @xmindltd/xmind-cli

# 2. Install skills into your agent's skill directory
npx skills add xmindltd/xmind-cli
```

The second command auto-detects your installed agents (Claude Code, Cursor, Codex, Gemini CLI, OpenCode, Windsurf, GitHub Copilot, Antigravity) and symlinks the skill files into the right place.

To install only specific skills:

```bash
npx skills add xmindltd/xmind-cli -s xmind-file,xmind-cloud
```

## Skills

| Skill | When to use | CLI surface |
|---|---|---|
| [`xmind-file`](skills/xmind-file/SKILL.md) | Local `.xmind` files (default) | `xmind create / read / add / update / theme / batch / image` |
| [`xmind-cloud`](skills/xmind-cloud/SKILL.md) | Real-time editing on [xmind.app](https://xmind.app) | `xmind cloud auth / open / batch / upload` |
| [`xmind-illustrated-map`](skills/xmind-illustrated-map/SKILL.md) | Mind maps with generated topic images (requires image-gen agent) | `xmind image-plan / enrich-images` |

This repository is the install entry — the three `SKILL.md` files distributed via `npx skills add` and the marketplace declaration in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json). The CLI binary and the knowledge it loads on demand ship via `npm install -g @xmindltd/xmind-cli`.

## Quick start

Once installed, just talk to your agent:

> *"Make a mind map about the evolution of transportation, with images for each era."*

The agent picks the right skill, plans the structure, generates content, and produces a `.xmind` file you can open in [Xmind](https://xmind.com).

## Links

- [Xmind](https://xmind.com) — desktop and cloud apps that read `.xmind` files
- [Agent Skills spec](https://github.com/anthropics/skills) — the format these skills follow

## License

MIT
