---
name: xmind-illustrated-map
description: Create Xmind mind maps with generated topic images via a contact-sheet workflow. Requires an image generation capability (built-in for Codex / Gemini CLI, or an external image API). Use for illustrated maps; for text-only maps, use xmind-file.
---

# Xmind Illustrated Map

## Prerequisites

- xmind CLI installed (`npm i -g @xmindltd/xmind-cli`)
- An image generation capability:
  - Codex / Gemini CLI: built-in `image_gen` works out of the box
  - Other agents: configure an external image API and produce a single contact-sheet image as a local file
- For text-only maps, stop here and use [xmind-file](../xmind-file/SKILL.md)

## How it works

The CLI is a post-processing tool. The agent owns image generation; the CLI plans slots, slices the local contact sheet, and embeds slices into `.xmind` resources.

```text
[xmind create] -> [xmind image-plan] -> [agent generates contact-sheet.png] -> [xmind enrich-images]
```

Minimal artifacts:

- `<file>.xmind` — the final map to enrich
- `plan.json` — visual slot mapping
- `prompt.txt` — debug copy of the prompt
- `contact-sheet.png` — final local raster file for attachment

Do not treat an inline preview or a remembered generated asset as completion. Attach only after `contact-sheet.png` exists as a real local file.

## Workflow

### 1. Pick the structure strategy

```bash
xmind skill list
```

Default routing:

- History, evolution, staged change: `timeline`
- Item index with rich notes: `cards`
- Matrix or table-like content: `comparison`
- Everything else: `default`

Then load the matching guidance:

```bash
xmind skill show domain/default render render/strategy/<name>
```

### 2. Build the markdown first

Write the map as markdown before generating images.

Rules:

- Keep the root title equal to the final map title.
- Prefer 4 to 6 strong first-level branches when the map will be illustrated.
- Put supporting detail in `###` and below.
- For history/evolution topics, aim for 5 eras plus the root hero so the visual plan becomes a clean 3x2 contact sheet.

### 3. Create the .xmind

```bash
cat <<'MD' | xmind create --stdin --skeleton <skeleton> --color <color> -o output.xmind
# Topic
## Branch
### Detail
MD
```

Recommended defaults:

- History/evolution: `Timeline-1` + `Aurora`
- General knowledge overview: `MindMap-1` + `Aurora`
- Item index: follow the `cards` strategy guidance

Verify immediately:

```bash
xmind read output.xmind
```

### 4. Plan image slots

```bash
xmind image-plan output.xmind \
  --grid-shape <auto|quad|timeline-strip|hero-branches|matrix> \
  --output plan.json \
  --prompt-output prompt.txt \
  --topics-output topics.json
```

Grid shape by content:

- `hero-branches` — root hero + major branches
- `timeline-strip` — chronological / phased
- `quad` — four-part structures (SWOT)
- `matrix` — two-axis comparison
- `auto` — fallback when no strong signal

Planning rules:

- Default target is the root plus key first-level branches.
- Keep image coverage selective. Do not try to illustrate every leaf.
- Treat the root image as a hero summary and branch images as visual anchors.
- If root or first-level branch titles change before attaching, rerun `image-plan`.

### 5. Generate one contact sheet

Use the agent's image generation capability with the contents of `prompt.txt`.

Prompt discipline:

- Generate one contact sheet, not one image per topic.
- Match the planned grid. For six slots, ask for `3 rows by 2 columns`.
- Require clear panel boundaries and uniform gutters so slicing is reliable.
- Require one consistent art direction across all panels.
- Require no text, numbers, watermarks, logos, or labels.

After generation, ensure the final raster exists as a local file:

```bash
contact-sheet.png
```

The image may initially be under `$HOME/.codex/generated_images/...`; move or copy it into the run directory before attaching.

### 6. Attach images

Use the saved plan:

```bash
xmind enrich-images output.xmind --use-plan plan.json --contact-sheet contact-sheet.png
```

Or use the current map directly for the common root-plus-branches case:

```bash
xmind enrich-images output.xmind --contact-sheet contact-sheet.png --limit 6
```

This step:

- slices the contact sheet locally
- attaches each slice to the planned topics
- writes images into `.xmind` `resources/*`
- cleans unreferenced old resources

### 7. Verify the result

```bash
xmind read output.xmind
unzip -l output.xmind
```

Success criteria:

- the root and planned first-level branches have images
- `content.json` uses `xap:resources/...`
- the zip contains matching `resources/*.png`
- panel order matches the intended topic order

## Example

For `The Evolution of Transportation`:

- Use `timeline`
- Create 5 historical eras as first-level branches
- Run `image-plan ... --output plan.json --prompt-output prompt.txt --topics-output topics.json`
- Generate one 3x2 contact sheet with the agent's image gen capability
- Put the generated raster at `contact-sheet.png`
- Run `xmind enrich-images output.xmind --use-plan plan.json --contact-sheet contact-sheet.png`
- Verify the `.xmind` zip contains 6 embedded resources
