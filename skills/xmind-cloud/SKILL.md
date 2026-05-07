---
name: xmind-cloud
description: Interact with Xmind online mind maps. Use when the user wants to read, edit, or create cloud mind maps in real-time.
---

# Xmind Cloud CLI

## Setup

```bash
which xmind || npm install -g @xmindltd/xmind-cli
```

## Auth

```bash
xmind cloud auth status
```

If `{"authenticated":false}`:

```bash
xmind cloud auth login
# → Opens browser automatically, waits for authorization
# → {"authenticated":true,"source":"config"}
```

Alternative (if you already have a token):

```bash
xmind cloud auth token <value>
```

## Session Rules

- **Reuse sessions**: before opening, check `xmind cloud sessions`. If the document already has an active session, reuse it.
- **Don't close after every edit**: sessions persist and auto-close after 30 minutes of inactivity. Only close when the user is done or explicitly asks.
- **Daemon persists across sessions**: closing a session doesn't kill the daemon. Next `open` is faster.

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
2. **Phase 2 — Render**: Follow render + strategy rules to produce markdown, select skeleton and color. **Substitute the execute command** with cloud upload:

   ```bash
   cat <<'MD' | xmind cloud create --stdin --skeleton <name> --color <name>
   # Central Topic
   ## Branch
   ### Sub-branch
   MD
   ```

   Returns `{"success":true,"fileId":"...","url":"https://app.xmind.com/<id>"}`.

If a subtree needs a different strategy during Phase 2:

```bash
xmind skill show render/strategy/<other>
```

## Edit

### Connect (only if no active session)

```bash
xmind cloud sessions
# If empty or no session for this document:
xmind cloud open <link-or-fileId>
# → {"session":"abc","fileId":"...","root":"..."}
```

### Quick Edit

```bash
xmind cloud read --session <id>
xmind cloud add --session <id> --parent <title> --topic <text>
xmind cloud update --session <id> --topic <old-title> --title <new-title>
xmind cloud delete --session <id> --topic <title>
cat <<'BATCH_EOF' | xmind cloud batch --session <id>
[{"op": "...", ...}]
BATCH_EOF
```

### Guided Edit

For complex edits (optimize, review, restructure, restyle), load the edit guide:

```bash
xmind skill show edit/guide
```

Follow the guide's analysis template, then apply changes via `xmind cloud batch --session <id>`.

## Upload Existing File

```bash
xmind cloud upload output.xmind
```

## Error Handling

- `{"error":"token_expired"}` → Token expired. Re-authenticate with `xmind cloud auth login`.
- `{"error":"Session not found"}` → Re-open: `xmind cloud open <link>`.
- `{"error":"Authentication failed"}` → Token invalid.
- `{"error":"exceed_files_limit"}` → Cloud storage full.
