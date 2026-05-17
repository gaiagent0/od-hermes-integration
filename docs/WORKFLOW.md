# Design Workflow Guide

## Recommended flow

1. Start Open Design from AI Control Center (WSL2 → Open Design → Start)
2. Select **Local CLI · Hermes** in the model picker (top right)
3. Choose model: `openrouter/nvidia/nemotron-3-nano-30b-a3b:free` or `groq-smart`
4. Import the target HTML file via **Import**
5. Use a structured prompt (see below)

## Effective prompts

### Skip discovery form

Always include `skip questions, just build` to bypass the 2-step form.

### Dashboard redesign template

```
skip questions, just build

Read the uploaded index.html in this project. Output a complete
redesigned version as a single artifact keeping ALL existing JS
and API calls unchanged.

Improve only the CSS:
- Animated glow on .dot.on
- Glassmorphism .svc-row:hover
- Gradient line under .section-header
- Gradient buttons
- Header stats bar showing running count

Colors (keep exactly):
--bg:#0e1117 --bg2:#161b27 --bg3:#1d2436 --bg4:#242b3d
--border:#2a3149 --green:#22c55e --red:#ef4444
--blue:#3b82f6 --purple:#a855f7 --amber:#f59e0b

Tabler Icons CDN. Hungarian UI text. Vanilla JS only.
Output ONLY the complete HTML file.
```

### New component template

```
skip questions, just build

Create a [component description] as a single self-contained HTML file.
Dark theme. Colors: --bg:#0e1117 --bg2:#161b27 --green:#22c55e --blue:#3b82f6
Tabler Icons CDN. Vanilla JS. Hungarian UI text.
```

## Model selection guide

| Task | Best model | Why |
|------|-----------|-----|
| Full dashboard redesign | `groq-smart` | 131K context, fast output |
| New component | `lmstudio-smart` | Local, private |
| Complex layout | `openrouter/nvidia/nemotron-3-nano-30b-a3b:free` | 256K context |
| Quick iteration | `groq-fast` | Fastest response |

## HyperFrames (video generation)

```bash
# Setup (one-time)
bash ~/.hermes/hermes-agent/optional-skills/creative/hyperframes/scripts/setup.sh

# Copy skill to Open Design
mkdir -p ~/open-design/skills/hyperframes
cp ~/.hermes/hermes-agent/optional-skills/creative/hyperframes/SKILL.md \
   ~/open-design/skills/hyperframes/SKILL.md

# Restart daemon to pick up skill
cd ~/open-design && pnpm tools-dev stop
export PATH="$HOME/.local/bin:$PATH"
pnpm tools-dev
```

In Open Design UI: Skills → hyperframes → select
