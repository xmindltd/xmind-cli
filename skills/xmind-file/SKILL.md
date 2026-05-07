---
name: xmind-file
description: Create and edit local .xmind mind map files via the xmind CLI. Use for text-only mind maps; for illustrated maps with generated images, use xmind-illustrated-map instead.
---

# Xmind File

## Setup

```bash
which xmind || npm install -g @xmindltd/xmind-cli
```

## Create

### Step 1: Discover

```bash
xmind skill list
```

Review all available domains, render strategies, and edit strategies.

### Step 2: Load Skills + Execute

Load domain, render, and strategy in one call:

```bash
xmind skill show domain/<name> render render/strategy/<name>
```

Then follow the loaded instructions:

1. **Phase 1 — Content**: Follow domain rules to plan content.
2. **Phase 2 — Render**: Follow render + strategy rules to produce markdown, select skeleton and color, execute CLI.

If a subtree needs a different strategy during Phase 2:

```bash
xmind skill show render/strategy/<other>
```

## Edit

### Quick Edit

```bash
xmind read <file>
xmind add <file> --parent <title> --topic <text>
xmind update <file> --topic <old-title> --title <new-title>
xmind delete <file> --topic <title>
xmind theme <file> --skeleton <name> --color <name>
xmind marker <file> --topic <title> --marker <id>
xmind label <file> --topic <title> --labels "Label1,Label2"
xmind layout <file> --topic <title> --layout <name>
cat <<'BATCH_EOF' | xmind batch <file>
[{"op": "...", ...}]
BATCH_EOF
```

### Guided Edit

For complex edits (optimize, review, restructure, restyle), load the edit guide:

```bash
xmind skill show edit/guide
```

Follow the guide's analysis template, then apply changes via batch.

## Manual Image Attach

If the user already has local image files, attach them directly to topics:

```bash
xmind image <file> --topic <title> --input <path>
xmind image <file> --topic <title> --remove
```

For illustrated maps where the agent generates images, use [xmind-illustrated-map](../xmind-illustrated-map/SKILL.md) instead.
