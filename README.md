<!-- markdownlint-disable-file MD041 -->

![Mind maps, by AI agents — xmind-cli](assets/xmind-file.png)

# xmind-cli

[![npm version](https://img.shields.io/npm/v/@xmindltd/xmind-cli.svg)](https://www.npmjs.com/package/@xmindltd/xmind-cli)
[![license: MIT](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

**Create local Xmind mind maps with AI agents.**

`xmind-cli` gives agents a reliable local-file workflow for `.xmind` maps:
generate structured maps, inspect and validate files, apply edits, attach
images, and report Xmind credits used by premium features.

## Install

```bash
npm install -g @xmindltd/xmind-cli
npx skills add xmindltd/xmind-cli -y
```

The skill install command adds the `xmind-file` skill to supported agent skill
directories. Verify the CLI and available guidance:

```bash
xmind skill list
xmind auth status
```

If needed, sign in once:

```bash
xmind auth login
```

## Quick Start

Ask your agent:

> Create a local Xmind mind map about Apollo 11.

The agent uses the `xmind-file` skill to choose a recipe, draft the map, build a
generate spec, run `xmind generate`, validate the `.xmind`, and summarize the
result.

## What It Can Do

- Generate rich local `.xmind` files from semantic specs.
- Create quick maps from Markdown.
- Read, describe, and validate existing `.xmind` files.
- Edit maps with add/update/delete/theme/layout/marker/label/batch commands.
- Attach local images, stable public raster image URLs, or Wikipedia images.
- Report actual billing as Xmind credits when premium features are used.

Core commands:

```bash
xmind generate --spec generate.json -o output.xmind
xmind generate check --spec generate.json
xmind create --from-markdown draft.md -o output.xmind
xmind read output.xmind
xmind describe output.xmind
xmind validate output.xmind --quiet
xmind batch output.xmind
xmind image output.xmind --topic "Topic" --wiki "Apollo 11"
```

## How Generation Works

The agent supplies semantic judgment. The CLI handles mechanical execution.

1. The agent chooses a recipe for the user goal.
2. The agent drafts Markdown with titles, hierarchy, and notes.
3. The agent writes a generate spec with route intent, visual anchors, and image
   targets.
4. `xmind generate` selects the final structure/color, applies anchors and
   images, validates the file, and returns JSON.
5. The agent reports the output path, validation result, key design choices, and
   any Xmind credits consumed.

The JSON output includes execution facts such as `route.skeletonReason`,
`route.colorReason`, `anchors.applied`, `billing.consumed`, `billing.balance`,
`billing.unit`, and `downgraded`.

## Billing

Some premium structures, themes, or recipe skills may consume Xmind credits.
The CLI reports billing in JSON. Treat `billing.unit` as authoritative; these
are **Xmind credits**, not OpenAI, Codex, model, or token credits.

If credits are unavailable, the CLI may downgrade to a free fallback unless the
agent uses `--no-downgrade`.

## This Repo

This repository distributes the public agent skill entry point:

- [`skills/xmind-file/SKILL.md`](skills/xmind-file/SKILL.md)
- [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json)

The CLI binary ships separately on npm as
[`@xmindltd/xmind-cli`](https://www.npmjs.com/package/@xmindltd/xmind-cli).

## Links

- [Xmind](https://xmind.com) — apps that read `.xmind` files
- [`@xmindltd/xmind-cli`](https://www.npmjs.com/package/@xmindltd/xmind-cli)
- [Agent Skills spec](https://github.com/anthropics/skills)

## License

MIT
