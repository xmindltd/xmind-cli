<!-- markdownlint-disable-file MD041 -->

![Mind maps, by AI agents — xmind-cli](assets/xmind-file.png)

# xmind-cli

[![npm version](https://img.shields.io/npm/v/@xmindltd/xmind-cli.svg)](https://www.npmjs.com/package/@xmindltd/xmind-cli)
[![license: MIT](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

**Mind maps, by AI agents.**

Create local `.xmind` files, edit cloud documents live on [app.xmind.com](https://app.xmind.com), and generate illustrated mind maps — all from your AI agent through a single CLI.

## Install

```bash
# 1. Install the CLI
npm install -g @xmindltd/xmind-cli

# 2. Install skills into your agent's skill directory
npx skills add xmindltd/xmind-cli -y
```

The second command auto-detects your installed agents (Claude Code, Cursor, Codex, Gemini CLI, OpenCode, Windsurf, GitHub Copilot, Antigravity) and symlinks the skill files into the right place (the `-y` flag skips prompts in non-interactive environments).

To install only specific skills:

```bash
npx skills add xmindltd/xmind-cli -s xmind-file,xmind-cloud -y
```

---

## What you can build

### Local `.xmind` files

A Domain Expert plans the structure; a Render Expert picks the skeleton and color theme. The result is a `.xmind` file.

> _"Create a mind map about Apollo 11."_

**`xmind create`** / `read` / `add` / `update` / `theme` / `batch` / `image`

**Skill:** [`xmind-file`](skills/xmind-file/SKILL.md)

---

### Live editing on app.xmind.com

![AI Agent Platform 2026 OKRs edited live on app.xmind.com](assets/xmind-cloud.png)

Read, edit, and create real-time documents on [app.xmind.com](https://app.xmind.com) — your agent appears as a co-editor on the same canvas as your team.

> _"Update the AI Agent Platform 2026 OKRs."_

**`xmind cloud open`** / `auth` / `batch` / `upload`

**Setup:** `xmind cloud auth login` once (browser auth) · **Skill:** [`xmind-cloud`](skills/xmind-cloud/SKILL.md)

---

### Illustrated mind maps

![12 Olympian gods illustrated mind map with AI-generated portraits](assets/xmind-illustrated.png)

A Domain Expert plans the structure; an Image Studio generates an AI illustration for every topic. The result is an illustrated `.xmind` file.

> _"Illustrated mind map of the 12 Olympian gods."_

**`xmind image-plan`** / `enrich-images`

**Requires:** image generation (Codex) · **Skill:** [`xmind-illustrated-map`](skills/xmind-illustrated-map/SKILL.md)

---

## How it works

Skills load on demand from your agent's skill directory. The roles named above (Domain Expert, Render Expert, Image Studio, Cloud Auth, Live Edit) are internal stages each skill bundles; the agent runs them in sequence.

The CLI binary (`xmind ...`) is the only external piece — after `npx skills add`, nothing else needs configuration.

---

## About this repo

This repo ships:

- The three `SKILL.md` files (distributed via `npx skills add`)
- The marketplace declaration in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json)

The CLI binary itself ships separately via [`@xmindltd/xmind-cli`](https://www.npmjs.com/package/@xmindltd/xmind-cli) on npm.

---

## Links

- [Xmind](https://xmind.com) — desktop and cloud apps that read `.xmind` files
- [`@xmindltd/xmind-cli`](https://www.npmjs.com/package/@xmindltd/xmind-cli) — the npm package
- [Agent Skills spec](https://github.com/anthropics/skills) — the format these skills follow

## License

MIT
