# Tokenomics Mind Map

An interactive mind map of Tokenomics: everything that goes into producing a token, consuming one, and turning it into value.

The content lives in a single YAML file ([tokenomics_mindmap.yaml](tokenomics_mindmap.yaml)). [mindmap.html](mindmap.html) is a self-contained D3 viewer and editor for it: no build step, no server required.

## Quick start

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

Turn on the **Edit** checkbox to modify the map:

- Double-click a node to rename it, edit its description, or attach links
- Click **add node** on a node to give it a child
- **Save YAML** writes back to the file you opened (Chrome/Edge, via the File System Access API) or downloads a copy elsewhere

## Structure

Three top-level branches, color-coded in the viewer:

- **Generation** (blue): the supply side. Tokenization, Model / Architecture, Hardware, Training, Serving.
- **Consumption** (purple): the demand side. Prompt / Context Engineering, Optimization, Workloads / Use Cases, Access & Delivery, Guardrails / Quality.
- **Value** (pink): the money. Pricing, Unit Economics, Cost Governance / FinOps, Monetization, Business Case, Observability.

## YAML format

Every node is a mapping key. `Description` and `Links` are optional; `children` nests the next level.

```yaml
Tokenomics:
  Description: "The full economics of producing and consuming LLM tokens."
  children:
    Generation:
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
