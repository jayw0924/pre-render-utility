# Metabox Batch Pre-Render

A browser-based tool for batch-rendering product/material combinations from a Metabox 3D configurator. Generates a ZIP of screenshots across multiple products, materials, camera angles, resolutions, and formats — no build step required.

## How It Works

Open `index.html` in a browser (localhost or HTTPS) and follow the 4-step workflow:

### 1. Load
Paste a Metabox configurator URL or UUID. The 3D viewport loads in the left panel.

### 2. Select
Choose which products and materials to include. The tool builds a cartesian product of all selected material slots — e.g. 3 colors x 2 fabrics = 6 permutations. Products are sorted to minimize expensive product switches during rendering.

### 3. Camera
Set the output resolution (which constrains the viewport to the correct aspect ratio), then position the camera and save named angles. Each permutation is rendered at every saved angle.

### 4. Render
Pick a format (PNG / JPEG / WebP), optionally estimate file size, then start the batch. Progress is shown in real-time. On completion, a ZIP is downloaded automatically.

## Render Order

The batch renders **angles as the outer loop** and **permutations as the inner loop**:

```
for each camera angle:
  set camera (once)
  for each product/material permutation:
    set materials -> wait for viewport ready -> screenshot
```

This ensures every screenshot is preceded by a material change that forces the 3D engine to re-render with the current camera position.

## Tech Stack

- **Single HTML file** — no build, no dependencies to install
- **Vue 3** via CDN (import map)
- **JSZip** via CDN for client-side ZIP generation
- **@3dsource/metabox-front-api** via CDN

## Known Issues

- **`SetCamera` one-behind bug** — The Metabox API's `SetCamera` command has a known bug where it applies the *previous* camera state instead of the current one. This affects both live preview and batch rendering of multiple camera angles. A fix is pending from the 3DSource team. Single-angle batches work correctly.

## Project Structure

```
pre-render/
  index.html                          # The batch pre-render tool
  CLAUDE.md                           # Claude Code project instructions
  metabox_configurator_basic_api.md   # Basic configurator API reference
  metabox_configurator_modular_api.md # Modular configurator API reference
  docs/
    CSS_PATTERNS.md                   # Critical CSS layout patterns
  templates/                          # Starter templates for new configurator UIs
    README.md                         # Template system documentation
    basic-configurator/
    modular-configurator/
```

## Running Locally

```bash
# Any local server works — the configurator requires localhost or HTTPS
python3 -m http.server 8000
# Open http://localhost:8000
```
