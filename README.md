# Tokenomics Mind Map

An interactive mind map of Tokenomics: everything that goes into producing a token, consuming one, and turning it into value.

The content lives in a single YAML file ([tokenomics_mindmap.yaml](tokenomics_mindmap.yaml)). [mindmap.html](mindmap.html) is a self-contained D3 viewer and editor for it: no build step, no server required.

## Quick start

**[Open the live map](https://tokenomics-foundation.github.io/tokenomics_mindmap/)** - hosted on GitHub Pages, nothing to install.

### Run it locally

The viewer auto-loads `tokenomics_mindmap.yaml` from its own folder over HTTP, so the simplest path is a local server:

```bash
git clone https://github.com/<you>/tokenomics_mindmap.git
cd tokenomics_mindmap
python3 -m http.server 8000
```

Open http://localhost:8000/mindmap.html and the map loads itself.

Opening `mindmap.html` directly from disk also works. Browsers block `fetch` on `file://`, so you get the loader screen instead of an auto-load: choose or drag in `tokenomics_mindmap.yaml`.

## Using the map

| Action | How |
| --- | --- |
| Zoom / pan | Scroll to zoom, drag to pan |
| Collapse a subtree | Click a node |
| Fit to screen | **Fit** |
| Expand everything | **Expand all** |
| Back to top level | **Collapse** |
| Find a node | Type in search, press Enter |
| Read a description | Hover a node |

The map opens with one level of detail under Production, Consumption, and Value. Click any node to reveal deeper levels.

### Showing what changed (diff mode)

Click **Compare…** and pick either:

- an **earlier YAML snapshot** of the map (the state at your last WG call), or
- a **git patch/diff** (`git diff last-tag-or-commit..HEAD -- tokenomics_mindmap.yaml > changes.patch`)

The map recolors: **green = added**, **red = removed** (shown as ghost nodes with strikethrough), **yellow = revised or moved**, gray = unchanged. Lines match their child node. Hovering a revised node shows the old description alongside the new one. A banner counts the changes; **Changed only** hides untouched subtrees, and **Exit diff** (or Escape) returns to the normal color-coded view.

When you feed in a patch, the viewer applies it to the loaded YAML (forward, or reversed if your diff runs the other way) before coloring. If a patch fails to apply cleanly, compare against the earlier YAML snapshot instead.

Turn on the **Edit** checkbox to modify the map:

- Double-click a node to rename it, edit its description, or attach links
- Click **add node** on a node to give it a child
- **Save YAML** writes back to the file you opened (Chrome/Edge, via the File System Access API) or downloads a copy elsewhere

## Kiosk mode

[kiosk.html](kiosk.html) is a separate, self-contained viewer designed for a **portrait 1080×1920 touchscreen** (event hallway deployments). It reads the same `tokenomics_mindmap.yaml` and is meant to run fullscreen in a locked-down browser (`chrome --kiosk --app=http://<host>:8000/kiosk.html`). Branding follows tokeneconomics.com (`#4F7CFF` on `#0A0B0F`, official logo vendored at `assets/tf-logo.svg`).

How it differs from `mindmap.html`:

- **Expandable vertical list**: concepts open in place as an indented list (not a wide dendrogram), so **font size never shrinks with tree depth**: deep trees scroll instead of squishing. Root/branch/concept tiers stay at 52/40/34 px at every depth.
- **Cover screen**: TF logo, brand-gradient title, animated label-free "breathing" dendrogram cycling through branches every ~14 s.
- **Guided tutorial**: a 3-step tip overlay on entry (skippable).
- **Persistent definition card**: the first touch of any node opens the definition panel and it stays up, updating as you explore; only the large ✕ dismisses it. Non-leaf nodes show their definition too, no more click-click-click to find it.
- **Feedback QR**: every card carries a QR deep-linking to the feedback form pre-filled with the node path. **Set `FEEDBACK_BASE` in kiosk.html to the live form URL before deploying** (works with any form accepting a `?node=` query param; degrades gracefully offline).
- **Idle reset**: 90 s without a touch returns to the cover; the toast says "touch anywhere to cancel".
- **Easter egg**: three consecutive precise taps on node bullets reveals TOKI, the Tokenomics mascot (ASCII placeholder pending the SVG).
- **Light on hardware**: no per-frame JavaScript at rest; list rows are trivial to render. Should be comfortable on the i7/UHD-graphics kiosk box; smoke-test on the real hardware before shipping.

The kiosk needs the YAML served over HTTP (same `python3 -m http.server 8000` as above), and network access for the D3/js-yaml CDN, or vendor both libraries locally and repoint the `<script>` tags for an offline kiosk.

**Opening `kiosk.html` directly from disk (file://):** browsers block the auto-load fetch, so the kiosk shows a load screen: pick or drag in `tokenomics_mindmap.yaml` once and everything works the same. For an unattended kiosk deployment, always serve the folder over HTTP so it loads hands-free.

## Structure

Three top-level branches, color-coded in the viewer:

- **Production** (blue): the supply side. Tokenization, Model / Architecture, Hardware, Training, Serving.
- **Consumption** (purple): the demand side. Prompt / Context Engineering, Optimization, Workloads / Use Cases, Access & Delivery, Guardrails / Quality.
- **Value** (pink): the money. Pricing, Unit Economics, Cost Governance / FinOps, Monetization, Business Case, Observability.

## YAML format

Every node is a mapping key. `Description` and `Links` are optional; `children` nests the next level.

```yaml
Tokenomics:
  Description: "The full economics of producing and consuming LLM tokens."
  children:
    Production:
      Description: "The supply side of tokens."
      children:
        PagedAttention:
          Description: "Pages the KV cache into fixed-size blocks."
          Links:
            - "https://blog.vllm.ai/2023/06/20/vllm.html"
```

`Description`, `Links`, and `children` are each accepted in either capitalization. A YAML file with a single root key renders that key as the root node; multiple top-level keys get wrapped in a generic "Mind Map" root.

## Dependencies

[D3 v7](https://d3js.org/) and [js-yaml v4](https://github.com/nodeca/js-yaml), both pulled from a CDN at page load. The viewer needs network access on first load, or you can vendor the two libraries locally and repoint the `<script>` tags.

## Contributing

Edits are YAML edits. Add or refine nodes in `tokenomics_mindmap.yaml`, keep descriptions to one plain sentence, and add a `Links` entry when a concept has a canonical paper or writeup. Open a PR with the diff.
