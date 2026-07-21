---
name: xmind-file
description: >-
  Create, validate, and edit local .xmind mind map files via the xmind CLI. Use
  for local structured knowledge maps, planning maps, and visual polish of
  existing .xmind files. This stage is local-file only with no cloud
  collaboration flow and no AI-generated-image workflow. Image attachments may
  use local user files or web-searched files downloaded to disk.
---

# Xmind File

Runtime protocol for local `.xmind` generation and editing.

The entry skill owns execution order and delivery gates. Recipes own content.
Visual owns semantic anchor planning. The CLI owns mechanical execution:
`generate`, file IO, billing/downgrade, anchors, images, and validation.

## Setup

```bash
which xmind || npm install -g "$(find ~/.claude /mnt/skills -name 'xmind-cli-*.tgz' 2>/dev/null | head -1)" 2>/dev/null || npm install -g @xmindltd/xmind-cli
xmind auth status
```

If unauthenticated, run `xmind auth login` or `xmind auth token <value>`.
File commands require login.

## Core Boundary

- Agent supplies semantic judgment: recipe, markdown, route intent, anchors, and
  image targets.
- `xmind generate` compiles the semantic spec into a `.xmind`: route gate,
  skeleton/color, billing/downgrade, create, anchors, images, validation, and
  JSON report.
- `xmind advise`, `create`, `batch`, `image`, and `validate` remain lower-level
  tools for quick maps, edits, repair, and inspection.
- Do not weaken content or visuals to avoid credits. Choose the right artifact;
  let the CLI charge, downgrade, or report.
- Sum turn credits from JSON stdout `billing.consumed`; missing `billing` means
  0.

## Recipe Choice

Run `xmind skill list` before drafting and treat its recipe catalog and triggers
as the source of truth. Do not maintain a recipe inventory in this skill.

Choose directly when the user names a recipe or you have high confidence that
one recipe best fits the requested artifact. Otherwise, recommend the best
match, present 2-4 relevant catalog choices with a short description of how each
would shape the artifact, and wait for the user to choose before drafting. When
confidence is not high, bias toward user choice.

This does not override CLI/server fallback. If `xmind skill show` returns
`effective: "quick-map"` because premium recipe access or Xmind credits are not
available, follow the effective recipe and explain the fallback plainly.

## Context Loading

| Need | Load |
|---|---|
| Discover capabilities | `xmind skill list` |
| Rich new map | `xmind skill show markdown-grammar recipe/<chosen> visual --json` |
| Quick free map | `xmind skill show markdown-grammar recipe/quick-map --json` |
| Existing-map edit | `xmind skill show edit/guide` |
| Low-level repair ops | `xmind skill show visual/layout-style visual/node-style` |
| Work-execution overlay | `xmind skill show task-overlay` |

For recipe JSON, record `requested`, `effective`, and `createSkill`. If the CLI
serves a fallback recipe, draft from `effective` and use its `createSkill`.

## Scratch IO

Use stable scratch paths under `/tmp`; do not use `$$`, `$RANDOM`, or `mktemp`
across commands.

- Draft: `/tmp/xmind-draft-<id>.md`
- Generate spec: `/tmp/xmind-generate-<id>.json`
- Downloaded/user images: real on-disk files that persist until attachment.

After successful delivery, delete only generated scratch markdown/spec. Never
delete final `.xmind`, user-provided inputs, or persistent downloaded assets
unless asked.

## Generate Spec

For rich generation, write a semantic spec and let the CLI compile it.

```json
{
  "version": 1,
  "markdown": { "path": "/tmp/xmind-draft-<id>.md" },
  "recipe": {
    "name": "<requested recipe>",
    "effective": "<effective recipe>",
    "createSkill": "<recipe item createSkill>"
  },
  "route": {
    "structuralType": "<radial_overview|mixed_modular|temporal_axis|procedural_flow|matrix_board|causal_ribs|hierarchy_tree|part_whole>",
    "structureCommitment": "<required|preferred|optional>",
    "toneTag": "<tech|nature|retro|elegant|fresh|sweet|vivid|plain>",
    "density": "<light|standard|deep>"
  },
  "baseline": {
    "skeleton": "auto",
    "color": "auto",
    "diversify": true,
    "seed": "<stable id>"
  },
  "anchors": {
    "layout": [],
    "group": [],
    "focus": [],
    "image": []
  }
}
```

Follow Visual for baseline selection, anchor-family semantics, target selection,
and its anchor quality gate.

## Semantic Quality Gate

Before `generate`, check the draft as content, not just as markdown:

- The chosen recipe matches the user goal; do not let `quick-map` replace a
  needed deep map.
- Top-level branches cover distinct facets and are not broad catch-all buckets.
- Important branches have visible child structure; notes do not carry the real
  hierarchy.
- Anchor targets already exist in honest content and can be found by title.
- The route fields describe the root relation, while anchors describe local
  exceptions.

If this gate fails, revise markdown/spec before running the CLI. Mechanical
validation cannot detect a shallow or semantically fake map.

## Flow A: Rich Generate

1. Load context:

   ```bash
   xmind skill list
   xmind skill show markdown-grammar recipe/<chosen> visual --json
   ```

2. Draft markdown from the effective recipe.

   Follow recipe density, shape, semantic signals, notes posture, and self-check.
   First draft the visible heading skeleton, then add selective notes and anchor
   candidates. Write pure CommonMark to `/tmp/xmind-draft-<id>.md`.

3. Write `/tmp/xmind-generate-<id>.json` using the route and baseline semantics
   defined by Visual.

4. Preflight and optionally dry-run:

   ```bash
   xmind generate check --spec /tmp/xmind-generate-<id>.json
   xmind generate --spec /tmp/xmind-generate-<id>.json --dry-run -o <file>.xmind
   ```

   Use dry-run for deep maps, premium-capable baselines, or when the baseline
   matters. If the route gate blocks, revise the same markdown/spec.

5. Generate:

   ```bash
   xmind generate --spec /tmp/xmind-generate-<id>.json -o <file>.xmind
   ```

   Treat stdout JSON as authoritative. If `validation.ok` is false or any
   `anchors.applied[].success` is false, fix the same markdown/spec and rerun.
   Passing CLI validation is necessary but not sufficient; also re-check the
   semantic gate before delivery.

6. Inspect only when needed:

   ```bash
   xmind read <file>.xmind
   xmind describe <file>.xmind
   xmind validate <file>.xmind --quiet
   ```

## Flow Q: Quick Free Generate

Use when the chosen recipe is `quick-map`.

1. Load:

   ```bash
   xmind skill list
   xmind skill show markdown-grammar recipe/quick-map --json
   ```

2. Draft compact markdown according to the effective recipe.

3. Choose the mode and free skeleton from the effective recipe's mode table.

4. Create and validate:

   ```bash
   xmind create --from-markdown /tmp/xmind-draft-<id>.md --skeleton <free-name> --color Dawn-#ffffff-MULTI_LINE_COLORS --skill <createSkill> -o <file>.xmind
   xmind validate <file>.xmind --quiet
   ```

   Run `read`/`describe` only if validation fails, the user asks, or a small
   repair is clearly needed.

5. Communicate the boundary.

   Tell the user this is a quick overview, not the full rich-map path. Offer to
   regenerate with a fuller recipe if they want deeper titles/notes, visual
   anchors, images, or premium-capable structure.

## Flow B: Edit Existing Map

1. Load `edit/guide`; load `visual` only if the edit changes visual treatment.
2. Inspect first:

   ```bash
   xmind read <file>.xmind
   xmind describe <file>.xmind
   xmind validate <file>.xmind --quiet
   ```

3. Plan against current structure and preserve unrelated user changes.
4. Use one `xmind batch` for multi-op edits; use direct commands for single
   edits.
5. Re-run `read`, `describe`, and `validate`.

## Images

Image inputs can be user-provided local files, stable raster image URLs, or
Wikipedia page images. Do not generate images as part of this skill. This
repository does not distribute a generated-image workflow.

Remote image URLs must be public raster images. SVG, private/local-network URLs,
and non-image payloads are rejected.

For Flow A, declare images in `anchors.image` so `xmind generate` attaches them
last. For edits:

```bash
xmind image <file>.xmind --topic "<topic title>" --input "<local-image-path>"
xmind image <file>.xmind --topic "<topic title>" --url "<https-image-url>"
xmind image <file>.xmind --topic "<topic title>" --wiki "<Wikipedia page title>"
```

## Links

Use `link` for a single topic. Local paths are resolved to absolute `file:///`
URIs and URL-encoded in the same form written by Xmind Desktop:

```bash
xmind link <map>.xmind --topic "Reference" --url "https://example.com"
xmind link <map>.xmind --topic "Local map" --file "../maps/example.xmind"
xmind link <map>.xmind --topic "Reference" --remove
```

For batch edits, `href` accepts either `href` or `file` (exactly one):

```json
{"op":"href","topic":"Local map","file":"../maps/example.xmind"}
```

Local file links point to the original file; they do not embed or copy it into
the `.xmind` archive. They work only on machines where that absolute path
exists. Use an `https` URL when the map must be portable.

## Delivery Gate

For Flow A:

- Effective recipe was loaded and followed.
- Generate spec points to the scratch markdown and includes `recipe.createSkill`.
- `xmind generate check` passed.
- Dry-run ran for non-trivial maps, or was skipped with a reason.
- Final generate report has `validation.ok: true`.
- All required `anchors.applied[].success` values are true.
- Billing was read from `billing.consumed`.
- Scratch markdown/spec were deleted after success.

For Flow Q:

- `recipe/quick-map` was loaded and followed.
- Free skeleton was used unless explicitly overridden.
- `createSkill` was passed to `xmind create --skill`.
- `xmind validate` has no structural errors.

## User Communication

For non-trivial generation, briefly explain the result in the user's language:

- Why the recipe/content shape fits the topic.
- Which visible nodes or groups motivated major visual features.
- Whether generation used Xmind credits. Never call them OpenAI, Codex, model,
  or token credits.

Use CLI JSON as source of truth, especially `route.skeletonReason`,
`route.colorReason`, `anchors.applied`, `billing.consumed`, `billing.balance`,
`billing.unit`, and `downgraded`. If `billing` is missing, treat the turn as 0
Xmind credits.

Offer one or two concrete next actions: deepen a branch, compare recipes, adjust
images/style, edit an existing `.xmind`, create a lighter/free version, or run
validation/inspection.

Final response: give the file path, validation result, and one or two important
design choices. Do not dump the full outline unless asked.

If the file was already open in Xmind, later CLI edits may not appear immediately;
tell the user to close and reopen it in Xmind to refresh.
