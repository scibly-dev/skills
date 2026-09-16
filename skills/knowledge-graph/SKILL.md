---
name: knowledge-graph
description: Builds an interactive, force-directed knowledge graph of a repo (or several repos at once) as a self-contained HTML page — every real document and source file becomes a node, real cross-references and real import statements become edges, and every node opens to that file's actual full content. Use when someone wants to see, map, get an overview of, or make navigable/explainable a codebase or docs repo; wants to onboard into unfamiliar code; asks "how does this all connect", "can you explain this repo to me", "show me a knowledge graph of this"; or asks whether several repos can be visualized/explored together (cross-repo mode). Not for a one-off diagram of a single flow or a small subsystem — this is for "the whole repo, browsable," not a single illustration.
---

# Knowledge Graph

Turns a repo — or several repos at once — into a browsable graph: every real
file is a node, every real cross-reference or import is an edge, and every
node opens to that file's actual full content. Nothing in the output is
invented; every node and edge traces back to a file that was actually read
or a statement that was actually parsed.

This skill embeds everything it produces — the viewer page and both Python
scripts — as complete code blocks below. Write them out verbatim into the
target repo, adapt the two TODO-marked sections in the `build_graph.py`
starter to that repo's real folders and language, then run it. Nothing here
needs a network fetch or a package install beyond a plain Python 3
interpreter; the viewer itself loads D3 and a Markdown renderer from a CDN
at view time.

## What the viewer can do

All of this lives in the one HTML file below, and nothing in it is specific
to any one repo — only the `graph.json` it loads is:

- **Force-directed graph**, nodes clustered by category (folder-derived),
  pan/zoom, drag.
- **Detail panel**: click a node for its real title, file path, a one- or
  two-sentence summary, and every real edge in or out.
- **Full-text reader**: "View full text" renders the node's actual file
  content — real Markdown as Markdown, real source code as an escaped,
  monospaced code block (never Markdown-rendered — that would mangle
  `Record<string, X>` and similar into broken HTML).
- **Clusters with drill-down**: a large, uniform group of files (e.g. 40
  near-identical content drafts) collapses into one dashed-border node;
  opening it shows a real grid of the real files inside, each still
  clickable through to its own full detail panel.
- **Category chips**: multi-select toggle to dim categories in or out.
- **Layer switcher**: a coarser preset row above the category chips (e.g.
  "Frontend / Backend / Docs / Config") that toggles a whole group of
  categories at once — only appears when at least one category declares a
  `"layer"` field.
- **Tests filter**: a single toggle that dims every file matching
  `\.test\.tsx?$` — cheap, filename-based, not a category.
- **Step-through**: ◄ / ► isolates exactly one category at a time with a
  position counter ("3 / 18: server"), for manually walking a rough
  approximation of a pipeline in the absence of real flow-tracing (see
  Limitations).
- **Cross-repo row**: when the loaded `graph.json` declares more than one
  repo, an extra "All repos / repo-a / repo-b" row appears above the layer
  switcher automatically — nothing to build per use, it's conditional on the
  data.
- **Search**, **reset**, dark-mode-aware theming, works standalone
  (double-click `viewer.html` to open) or published as a static page.

## The two extraction tracks — pick per folder, not per repo

Every file ends up in the graph through exactly one of these. Most repos
need both, applied to different parts of themselves.

### 1. Docs track — curated, for anything meant to be read

READMEs, design docs, ADRs, runbooks, tickets — anything where the *value*
is in what it says, not just in what it points at.

Read every file in full. Write a real one- or two-sentence summary (what it
actually says, not a rephrase of its title) and add an edge for every real
cross-reference you find — an explicit link, a "see docs/X.md", a named file
path, a "Ticket N" that another doc also names. Do not add an edge you can't
point to a sentence for.

This does not scale by reading faster; it scales by being honest about
scope. On a repo with 40 near-identical files (e.g. a folder of drafts, or
25 generic reference pages), do not write 40 individual summaries —
collapse them into one **cluster** node (`type: "cluster"`, `childCount: N`)
with real `children` entries (real title + real file path per child,
summary can equal the title when the title already says everything). The
viewer's drill-down exists for exactly this case. Reserve individual nodes
for files that are actually distinct from their siblings.

Mark a decision as superseded/contradicted with `"contra": true` on the edge
that says so — the viewer renders it dashed. Only for genuine "X replaces Y"
or "X marks Y outdated" relationships, not general disagreement.

### 2. Code track — structural, for source trees too large to read file by file

For anything where the value is in *how it connects*, not in prose — a
`src/` tree, a services layout — do not read every file for meaning.
Extract real `import`/`require`/`from ... import` statements with a regex,
resolve them against the language's own module-resolution rules (a path
alias from `tsconfig.json`, a relative import, a Python package layout),
and keep only the ones that resolve to another real file in the same tree —
an import of an external package is not an edge. Skip anything an
editor/IDE-standard resolver would skip.

This is deliberately not an LLM task: it should be exact and mechanical. The
`build_graph.py` template below has a complete, working TypeScript
implementation (`IMPORT_RE`, `resolve_ts_import`) to adapt; the comment next
to it sketches the equivalent Python shape.

A node from this track gets an honest, non-invented summary — "imports N
internal modules" (a fact, computed) — not a guess at what the file *does*.
If a specific file turns out to matter (central, highly connected, asked
about), read it and hand-write a real summary/edges for it individually;
promoting one node from track 2 to track 1 quality is normal and welcome.

### Category and layer, decided once per repo

Map folder → category (e.g. `src/server/api/` → `server-api`) in one
function — `category_for()` in the template. If the repo has a real
architectural split worth a coarse toggle (frontend vs. backend; app vs.
infra), add a `"layer"` key to the relevant categories in the `categories`
dict; if it doesn't (a docs/business repo usually doesn't), leave `"layer"`
off every category and the layer-switcher row simply won't render — don't
force a split that isn't real.

## Building one (single repo)

1. Inventory the repo: what's docs, what's code, roughly how many files of
   each, whether there's a path-alias config to resolve against.
2. Create a working directory in the target repo (e.g. `tools/knowledge-graph/`)
   and write out the three files below verbatim: `viewer.template.html`,
   `build_viewer.py`, and `build_graph.py` (start from the
   `build_graph.py` template and adapt every `# TODO` for the real repo —
   `doc_nodes`/`doc_edges` from files you actually read, `category_for()`
   from the real folder layout, the import regex/resolution for the real
   language).
3. Run `python3 build_graph.py` — it prints node/edge counts, a
   duplicate-id check and a missing-reference check. Both must read clean;
   they catch exactly the bugs this kind of hand-authored data tends to
   have.
4. Run `python3 build_viewer.py` — produces `viewer.html`, ready to open
   directly (double-click) or serve as a static page.
5. Look at it. Click a doc node, a code node if present, a cluster if
   present; open "View full text" on one of each; confirm a category toggle
   and (if present) the layer switcher visibly dim/restore nodes. `.md`
   content should render as real Markdown; `.ts`/`.py` content should
   render as plain escaped code, not be mangled by Markdown parsing.

Embedding full file content pushes `graph.json`/the viewer to a few MB for
a few-hundred-file repo — normal, not a problem for a static HTML file.

## Cross-repo mode

Build each repo's `graph.json` independently first (steps above, once per
repo) — this keeps every repo's graph independently useful and correct on
its own, cross-repo or not. Then:

1. Write out `merge_repos.py` (below) and fill in `REPOS`: a `(slug, label,
   path-to-that-repo's-graph.json)` pair per repo. It namespaces every node
   id and category key with `slug::`/`slug__` (so two repos' identically
   named `docs` categories, or a coincidentally identical file path, never
   collide), tags every node with `repo: slug`, and writes one merged
   `graph.json` with a top-level `repos: {slug: {label, source_repo}}` map.
2. **Cross-repo edges are not auto-detected** — there is usually no import
   statement across a repo boundary to regex for. List the real ones by
   hand in `CROSS_REPO_EDGES`, as `(from_slug, from_id, to_slug, to_id,
   rel)`, using each repo's own (un-prefixed) node ids; the script prefixes
   them. Add exactly the ones you can verify. A cross-repo graph with zero
   verified edges is still a legitimate, useful output — it just means the
   repos are genuinely independent, which is itself the finding.
3. Copy `viewer.template.html` + `build_viewer.py` next to the merged
   `graph.json` and run `build_viewer.py` as in the single-repo case. The
   viewer detects `GRAPH_DATA.repos` at load and renders the repo-filter row
   automatically — no template changes needed for cross-repo vs.
   single-repo.

Node/edge counts roughly add across repos (a 250-node repo + a 200-node
repo → a ~450-node merged graph); at that scale the force layout needs
slightly softer forces to stay legible than a much smaller graph does — if
nodes clump into an unreadably tiny cluster after merging, the anchor-force
strength in `viewer.template.html` (`d3.forceX/forceY(...).strength(...)`,
two call sites) is the first thing to check. The values shipped below were
tuned against a real ~450-node merged graph and are a reasonable starting
point; they also fix a real bug (see the comment inside `fitToContent`)
where a *very small* graph could settle before the page's own layout had a
real size, permanently zooming to nothing — the fix retries the fit instead
of committing to it, so it's safe at any repo size from a handful of files
up.

## What this deliberately does not do

- **No runtime/request-flow tracing.** The code track is an import graph —
  who references whom — not a call graph and not "what happens when a user
  clicks X." The step-through control is a manual, approximate stand-in for
  following a pipeline (step through the categories a request would
  plausibly pass through, reading each node's real content along the way),
  not real tracing. Building real call-graph tracing would mean AST parsing
  per language rather than regex-over-imports — a substantially larger
  undertaking, out of scope here.
- **No automatic cross-repo edges.** By design — an invented cross-repo
  connection is worse than a missing one.
- **No re-summarization on a schedule.** `graph.json` is a snapshot; rerun
  `build_graph.py` after real changes to the source repo, don't try to diff
  and patch it incrementally.

## `viewer.template.html`

The page. Copy verbatim; only the embedded `graph.json` (via
`build_viewer.py`) makes it repo-specific. Replace `__REPO_NAME__` in the
`<title>` with the real repo name (or leave it — the `<h1>` is set from
`graph.json` at load time regardless).

```html
<title>Knowledge Register __REPO_NAME__</title>
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,400;9..144,600;9..144,900&family=Archivo:wght@400;500;600;700&family=IBM+Plex+Mono:wght@400;500&display=swap">
<style>
  :root{
    --bg:#eef0f2;
    --surface:#ffffff;
    --surface-2:#e3e6ea;
    --ink:#14213a;
    --ink-muted:#52596b;
    --border:#c9ced6;
    --accent:#2f5f8a;
    --edge:#9aa1ac;
    --edge-contra:#b5533c;
    --shadow: 0 1px 2px rgba(20,33,58,0.06), 0 6px 20px rgba(20,33,58,0.08);

    --cat-meta:#52565c;--cat-entscheidung:#b5533c;--cat-positionierung:#2f5f8a;--cat-discovery:#2f8a86;
    --cat-foerderung:#a9822f;--cat-wettbewerb:#3f7a55;--cat-research:#3a7ca5;--cat-content:#7a4c72;
    --cat-skills:#7a6a52;--cat-legal:#8a3b4a;--cat-events:#b3701f;--cat-marketing:#c06a4f;
    --cat-demos:#4a5a9e;--cat-tooling:#5c6570;--cat-extern:#6650a3;
  }
  @media (prefers-color-scheme: dark){
    :root:not([data-theme="light"]){
      --bg:#14171c; --surface:#1c2027; --surface-2:#262b33; --ink:#e9e7df; --ink-muted:#a7acb6;
      --border:#343b45; --accent:#6fa8d8; --edge:#4a515c; --edge-contra:#e08064;
      --shadow: 0 1px 2px rgba(0,0,0,0.3), 0 10px 30px rgba(0,0,0,0.35);
      --cat-meta:#9a9fa6;--cat-entscheidung:#e08064;--cat-positionierung:#6fa8d8;--cat-discovery:#5fc4c0;
      --cat-foerderung:#d4ac5a;--cat-wettbewerb:#74b98c;--cat-research:#7fb8db;--cat-content:#c586ba;
      --cat-skills:#b3a284;--cat-legal:#cf7b8a;--cat-events:#e0a256;--cat-marketing:#e59677;
      --cat-demos:#8d9ade;--cat-tooling:#9aa5b1;--cat-extern:#a58ce0;
    }
  }
  :root[data-theme="dark"]{
    --bg:#14171c; --surface:#1c2027; --surface-2:#262b33; --ink:#e9e7df; --ink-muted:#a7acb6;
    --border:#343b45; --accent:#6fa8d8; --edge:#4a515c; --edge-contra:#e08064;
    --shadow: 0 1px 2px rgba(0,0,0,0.3), 0 10px 30px rgba(0,0,0,0.35);
    --cat-meta:#9a9fa6;--cat-entscheidung:#e08064;--cat-positionierung:#6fa8d8;--cat-discovery:#5fc4c0;
    --cat-foerderung:#d4ac5a;--cat-wettbewerb:#74b98c;--cat-research:#7fb8db;--cat-content:#c586ba;
    --cat-skills:#b3a284;--cat-legal:#cf7b8a;--cat-events:#e0a256;--cat-marketing:#e59677;
    --cat-demos:#8d9ade;--cat-tooling:#9aa5b1;--cat-extern:#a58ce0;
  }

  *{box-sizing:border-box;}
  html,body{height:100%;}
  body{margin:0; background:var(--bg); color:var(--ink); font-family:'Archivo',-apple-system,'Segoe UI',sans-serif;}
  .app{display:flex; flex-direction:column; height:100dvh; min-height:560px;}

  header{padding:16px clamp(16px,3vw,28px) 10px; border-bottom:1px solid var(--border); display:flex; flex-direction:column; gap:8px; flex-shrink:0;}
  .title-row{display:flex; align-items:baseline; gap:14px; flex-wrap:wrap;}
  h1{font-family:'Fraunces',serif; font-weight:600; font-size:clamp(19px,2.4vw,26px); margin:0; letter-spacing:-0.01em;}
  .stand{font-family:'IBM Plex Mono',monospace; font-size:11px; color:var(--ink-muted); white-space:nowrap;}
  .subtitle{font-size:12.5px; color:var(--ink-muted); max-width:82ch; line-height:1.5;}
  .controls{display:flex; gap:9px; align-items:center; flex-wrap:wrap;}
  #search{font-family:'Archivo',sans-serif; font-size:13px; padding:7px 12px; border-radius:7px; border:1px solid var(--border); background:var(--surface); color:var(--ink); min-width:170px; flex:0 1 210px;}
  #search:focus{outline:2px solid var(--accent); outline-offset:1px;}
  .chips{display:flex; gap:5px; flex-wrap:wrap;}
  .chip{font-family:'Archivo',sans-serif; font-size:11px; font-weight:600; padding:4px 9px 4px 7px; border-radius:999px; border:1px solid var(--border); background:var(--surface); color:var(--ink-muted); cursor:pointer; display:inline-flex; align-items:center; gap:5px; transition:opacity .15s ease, transform .1s ease; user-select:none;}
  .chip:hover{transform:translateY(-1px);}
  .chip .dot{width:7px;height:7px;border-radius:50%;flex-shrink:0;}
  .chip.off{opacity:.35;}
  .reset-btn{font-family:'Archivo',sans-serif; font-size:11.5px; font-weight:600; padding:6px 12px; border-radius:999px; border:1px solid var(--border); background:transparent; color:var(--ink-muted); cursor:pointer;}
  .reset-btn:hover{color:var(--ink); border-color:var(--ink-muted);}
  .controls2{display:flex; gap:9px; align-items:center; flex-wrap:wrap; margin-top:8px;}
  .layer-group{display:inline-flex; border:1px solid var(--border); border-radius:999px; overflow:hidden;}
  .layer-btn{font-family:'Archivo',sans-serif; font-size:11.5px; font-weight:600; padding:6px 11px; border:none; border-right:1px solid var(--border); background:var(--surface); color:var(--ink-muted); cursor:pointer;}
  .layer-btn:last-child{border-right:none;}
  .layer-btn:hover{color:var(--ink);}
  .layer-btn.active{background:var(--accent); color:var(--surface);}
  .tests-toggle{font-family:'Archivo',sans-serif; font-size:11.5px; font-weight:600; padding:6px 12px; border-radius:999px; border:1px solid var(--border); background:var(--surface); color:var(--ink-muted); cursor:pointer;}
  .tests-toggle.active{background:var(--edge-contra); border-color:var(--edge-contra); color:var(--surface);}
  .stepper{display:inline-flex; align-items:center; gap:6px; border:1px solid var(--border); border-radius:999px; padding:3px 4px 3px 10px; background:var(--surface);}
  .stepper button{font-family:'Archivo',sans-serif; font-weight:700; border:none; background:var(--surface-2); color:var(--ink); border-radius:999px; width:22px; height:22px; cursor:pointer; line-height:1;}
  .stepper button:hover{background:var(--accent); color:var(--surface);}
  .stepper button:disabled{opacity:.4; cursor:default;}
  .stepper button:disabled:hover{background:var(--surface-2); color:var(--ink);}
  #stepLabel{font-size:11.5px; color:var(--ink-muted); min-width:150px; text-align:center;}
  #stepLabel.active{color:var(--ink); font-weight:600;}
  #stepExit{width:auto; padding:0 8px; font-size:12px;}
  .count{font-family:'IBM Plex Mono',monospace; font-size:11px; color:var(--ink-muted); margin-left:auto; white-space:nowrap;}

  main{flex:1; display:grid; grid-template-columns:1fr 330px; min-height:0;}
  #graph-wrap{position:relative; overflow:hidden; background: radial-gradient(circle at 1px 1px, var(--border) 1px, transparent 1px) 0 0/26px 26px, var(--bg);}
  svg{display:block;}

  .node{cursor:grab;}
  .node:active{cursor:grabbing;}
  .node rect.card{fill:var(--surface); stroke:var(--border); stroke-width:1.1;}
  .node rect.stripe{stroke:none;}
  .node.is-cluster rect.card{stroke-dasharray:3 2; stroke-width:1.4;}
  .node.is-extern rect.card{stroke-dasharray:3 2;}
  .node.is-superseded text.label{text-decoration:line-through; text-decoration-color:var(--ink-muted);}
  .node text.label{font-family:'Archivo',sans-serif; font-weight:600; font-size:10px; fill:var(--ink); pointer-events:none;}
  .node text.cat-tag{font-family:'IBM Plex Mono',monospace; font-size:7.6px; letter-spacing:.03em; pointer-events:none;}
  .node.selected rect.card{stroke-width:2.2;}
  .edge-line{fill:none; stroke:var(--edge); stroke-width:1.1;}
  .edge-line.contra{stroke:var(--edge-contra); stroke-dasharray:4 3;}
  .edge-line.hi{stroke-width:2.2;}
  .edge-label{font-family:'IBM Plex Mono',monospace; font-size:7.8px; fill:var(--ink-muted); pointer-events:none;}
  .zoom-hint{position:absolute; left:14px; bottom:12px; font-family:'IBM Plex Mono',monospace; font-size:10.5px; color:var(--ink-muted); background:color-mix(in srgb, var(--surface) 82%, transparent); padding:4px 9px; border-radius:6px; border:1px solid var(--border);}
  .legend-note{position:absolute; right:14px; bottom:12px; max-width:300px; font-size:10.5px; line-height:1.5; color:var(--ink-muted); background:color-mix(in srgb, var(--surface) 82%, transparent); padding:6px 10px; border-radius:6px; border:1px solid var(--border);}
  .legend-note .swatch{display:inline-block;width:14px;height:0;border-top:2px dashed var(--edge-contra);vertical-align:middle;margin:0 3px;}

  /* drill-down + reader overlays */
  #drill,#reader{position:absolute; inset:0; background:var(--bg); display:none; flex-direction:column; z-index:5;}
  #drill.open,#reader.open{display:flex;}
  #drill-header,#reader-header{padding:16px 20px 10px; border-bottom:1px solid var(--border); flex-shrink:0;}
  #drill-back,#reader-back{font-family:'Archivo',sans-serif; font-size:12px; font-weight:700; border:none; background:none; color:var(--accent); cursor:pointer; padding:0; margin-bottom:8px; display:inline-flex; align-items:center; gap:4px;}
  #drill-title,#reader-title{font-family:'Fraunces',serif; font-weight:600; font-size:18px; margin:0 0 4px;}
  #drill-desc,#reader-desc{font-size:12.5px; color:var(--ink-muted); line-height:1.5; max-width:74ch;}
  #reader-desc{font-family:'IBM Plex Mono',monospace; font-size:11px;}
  #drill-grid{flex:1; overflow-y:auto; padding:16px 20px 24px; display:grid; grid-template-columns:repeat(auto-fill,minmax(230px,1fr)); gap:10px; align-content:start;}
  #reader-body{flex:1; overflow-y:auto; padding:18px 22px 40px;}
  .md-body{font-size:13px; line-height:1.7; max-width:76ch; color:var(--ink);}
  .md-body h1{font-family:'Fraunces',serif; font-weight:600; font-size:21px; margin:0 0 12px; text-wrap:balance;}
  .md-body h2{font-family:'Fraunces',serif; font-weight:600; font-size:16.5px; margin:24px 0 8px;}
  .md-body h3{font-family:'Fraunces',serif; font-weight:600; font-size:14px; margin:18px 0 6px;}
  .md-body p{margin:0 0 12px;}
  .md-body ul,.md-body ol{margin:0 0 12px; padding-left:22px;}
  .md-body li{margin:0 0 4px;}
  .md-body a{color:var(--accent);}
  .md-body strong{font-weight:700;}
  .md-body code{font-family:'IBM Plex Mono',monospace; font-size:12px; background:var(--surface-2); padding:1px 5px; border-radius:4px;}
  .md-body pre{background:var(--surface-2); padding:10px 12px; border-radius:8px; overflow-x:auto; margin:0 0 14px;}
  .md-body pre code{background:none; padding:0;}
  .md-body blockquote{border-left:3px solid var(--border); margin:0 0 14px; padding:2px 0 2px 12px; color:var(--ink-muted);}
  .md-body table{border-collapse:collapse; margin:0 0 16px; font-size:12px; display:block; overflow-x:auto;}
  .md-body th,.md-body td{border:1px solid var(--border); padding:5px 9px; text-align:left;}
  .md-body hr{border:none; border-top:1px solid var(--border); margin:20px 0;}
  .md-body img{max-width:100%;}
  .drill-card{border:1px solid var(--border); background:var(--surface); border-radius:9px; padding:10px 12px; cursor:pointer; transition:border-color .12s ease, transform .12s ease;}
  .drill-card:hover{border-color:var(--accent); transform:translateY(-1px);}
  .drill-card .stripe{height:3px; border-radius:2px; margin:-2px 0 8px; width:28px;}
  .drill-card .t{font-weight:600; font-size:12.5px; line-height:1.35; margin-bottom:5px;}
  .drill-card .f{font-family:'IBM Plex Mono',monospace; font-size:9.5px; color:var(--ink-muted); word-break:break-all; margin-bottom:5px;}
  .drill-card .s{font-size:11px; color:var(--ink-muted); line-height:1.45;}
  .drill-card.is-dim{opacity:.25;}

  /* ---------- detail panel ---------- */
  #panel{border-left:1px solid var(--border); background:var(--surface); padding:18px 18px 20px; overflow-y:auto; display:flex; flex-direction:column; gap:13px;}
  #panel .empty{color:var(--ink-muted); font-size:12.5px; line-height:1.65;}
  #panel .empty h2{font-family:'Fraunces',serif; font-size:16px; font-weight:600; margin:0 0 4px; color:var(--ink);}
  #panel .empty dl{margin:12px 0 0; display:grid; grid-template-columns:auto 1fr; gap:4px 10px; font-size:11.5px;}
  #panel .empty dt{color:var(--ink-muted);}
  #panel .empty dd{margin:0; font-family:'IBM Plex Mono',monospace;}
  .cat-chip{display:inline-flex; align-items:center; gap:6px; font-family:'IBM Plex Mono',monospace; font-size:10.5px; letter-spacing:.03em; text-transform:uppercase; color:var(--ink-muted);}
  .cat-chip .dot{width:9px;height:9px;border-radius:50%;}
  #panel h2{font-family:'Fraunces',serif; font-weight:600; font-size:17px; margin:0; line-height:1.3;}
  .filepath{font-family:'IBM Plex Mono',monospace; font-size:11px; color:var(--ink); background:var(--surface-2); padding:6px 9px; border-radius:6px; word-break:break-all;}
  .status-tag{display:inline-block; font-family:'IBM Plex Mono',monospace; font-size:10px; padding:2px 7px; border-radius:4px; background:var(--surface-2); color:var(--ink-muted); width:fit-content;}
  .status-tag.superseded{color:var(--edge-contra); border:1px solid var(--edge-contra);}
  #panel p.summary{font-size:12.5px; line-height:1.6; margin:0; color:var(--ink);}
  .drill-open-btn{font-family:'Archivo',sans-serif; font-size:12px; font-weight:700; padding:8px 12px; border-radius:8px; border:1px dashed var(--accent); background:transparent; color:var(--accent); cursor:pointer; text-align:left;}
  .drill-open-btn:hover{background:color-mix(in srgb, var(--accent) 10%, transparent);}
  #panel .rel-heading{font-family:'Archivo',sans-serif; font-weight:700; font-size:10.5px; text-transform:uppercase; letter-spacing:.04em; color:var(--ink-muted); margin:2px 0 -4px;}
  .rel-list{list-style:none; margin:0; padding:0; display:flex; flex-direction:column; gap:6px;}
  .rel-item{border:1px solid var(--border); border-radius:7px; padding:7px 9px; cursor:pointer; background:var(--bg); transition:border-color .12s ease;}
  .rel-item:hover{border-color:var(--accent);}
  .rel-item .rel-label{font-size:11px; color:var(--ink-muted); font-style:italic;}
  .rel-item .rel-target{font-size:12px; font-weight:600;}
  .rel-item.contra{border-left:3px solid var(--edge-contra);}
  #close-panel{align-self:flex-start; font-family:'Archivo',sans-serif; font-size:11.5px; font-weight:600; border:1px solid var(--border); background:transparent; color:var(--ink-muted); padding:5px 10px; border-radius:999px; cursor:pointer; display:none;}

  @media (max-width:760px){
    main{grid-template-columns:1fr;}
    #panel{position:fixed; left:0; right:0; bottom:0; max-height:66dvh; border-left:none; border-top:1px solid var(--border); border-radius:16px 16px 0 0; box-shadow:var(--shadow); transform:translateY(100%); transition:transform .22s ease; z-index:20;}
    #panel.open{transform:translateY(0);}
    #close-panel{display:inline-block;}
    .legend-note{display:none;}
    .count{display:none;}
    #drill-grid{grid-template-columns:1fr;}
  }
</style>

<div class="app">
  <header>
    <div class="title-row">
      <h1 id="h1-title">Knowledge Register</h1>
      <span class="stand" id="stand"></span>
    </div>
    <p class="subtitle" id="subtitle"></p>
    <div class="controls">
      <input id="search" type="text" placeholder="Search documents…" autocomplete="off">
      <div class="chips" id="chips"></div>
      <button class="reset-btn" id="reset">Reset filters &amp; layout</button>
      <span class="count" id="count"></span>
    </div>
    <div class="controls2">
      <div class="layer-group" id="repoGroup" style="display:none;"></div>
      <div class="layer-group" id="layerGroup"></div>
      <button class="tests-toggle" id="testsToggle">Hide tests</button>
      <div class="stepper">
        <button id="stepPrev" title="Previous category">◄</button>
        <span id="stepLabel">Step through</span>
        <button id="stepNext" title="Next category">►</button>
        <button id="stepExit" title="Exit single-category mode">✕</button>
      </div>
    </div>
  </header>
  <main>
    <div id="graph-wrap">
      <svg id="svg"></svg>
      <div id="drill">
        <div id="drill-header">
          <button id="drill-back">← Back to graph</button>
          <h2 id="drill-title"></h2>
          <div id="drill-desc"></div>
        </div>
        <div id="drill-grid"></div>
      </div>
      <div id="reader">
        <div id="reader-header">
          <button id="reader-back">← Back to graph</button>
          <h2 id="reader-title"></h2>
          <div id="reader-desc"></div>
        </div>
        <div id="reader-body"><div class="md-body" id="reader-md"></div></div>
      </div>
      <div class="zoom-hint">Drag to pan · Scroll to zoom · Nodes are draggable</div>
      <div class="legend-note"><span class="swatch"></span>dashed edge = the source document explicitly marks the target as outdated/replaced · dashed border = cluster, click to expand</div>
    </div>
    <aside id="panel">
      <div class="empty" id="panel-empty"></div>
    </aside>
  </main>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/d3/7.9.0/d3.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/marked/4.3.0/marked.min.js"></script>
<script>
const GRAPH_DATA = /*__GRAPH_DATA__*/{}/*__END_GRAPH_DATA__*/;
(function(){

const categories = GRAPH_DATA.categories;
const catOrder = Object.keys(categories);
catOrder.forEach(c => { categories[c].var = '--cat-' + c; });
const allNodes = GRAPH_DATA.nodes;
const allEdges = GRAPH_DATA.edges;

const repos = GRAPH_DATA.repos || null; // present + >1 entries in cross-repo mode
const repoTitle = repos ? Object.values(repos).map(r => r.label).join(' + ') : GRAPH_DATA.source_repo;
document.getElementById('h1-title').textContent = 'Knowledge Register — ' + repoTitle;
document.getElementById('stand').textContent = 'As of ' + GRAPH_DATA.generated;
document.getElementById('subtitle').textContent = GRAPH_DATA.methodology;

const nodes = allNodes.filter(n => !n.parent); // top-level graph
const edges = allEdges.filter(e => {
  const a = allNodes.find(n=>n.id===e.from), b = allNodes.find(n=>n.id===e.to);
  return a && b && !a.parent && !b.parent;
});
edges.forEach(e => { e.source = e.from; e.target = e.to; });

const nodeById = new Map(allNodes.map(n => [n.id, n]));
const neighbors = new Map(allNodes.map(n => [n.id, new Set()]));
const incident = new Map(allNodes.map(n => [n.id, []]));
allEdges.forEach(e => {
  if (!neighbors.has(e.from) || !neighbors.has(e.to)) return;
  neighbors.get(e.from).add(e.to);
  neighbors.get(e.to).add(e.from);
  incident.get(e.from).push(e);
  incident.get(e.to).push(e);
});

const childrenOf = new Map();
allNodes.forEach(n => { if (n.parent){ if(!childrenOf.has(n.parent)) childrenOf.set(n.parent, []); childrenOf.get(n.parent).push(n); } });

// ---------- cross-repo filter (only rendered with >1 repo loaded) ----------
const repoOff = {};
const repoBtnEls = [];
if (repos && Object.keys(repos).length > 1) {
  const repoGroupEl = document.getElementById('repoGroup');
  repoGroupEl.style.display = 'inline-flex';
  Object.keys(repos).forEach(slug => { repoOff[slug] = false; });
  const makeBtn = (label, slug) => {
    const btn = document.createElement('button');
    btn.className = 'layer-btn';
    btn.textContent = label;
    btn.addEventListener('click', () => {
      exitSolo();
      Object.keys(repos).forEach(s => { repoOff[s] = slug !== null && s !== slug; });
      syncUI(); render();
    });
    repoGroupEl.appendChild(btn);
    repoBtnEls.push({btn, slug});
  };
  makeBtn('All repos', null);
  Object.entries(repos).forEach(([slug, meta]) => makeBtn(meta.label, slug));
}
function isRepoHidden(d){ return !!(repos && d.repo && repoOff[d.repo]); }

// ---------- chrome: chips + counts ----------
const chipsEl = document.getElementById('chips');
const catOff = {};
let soloIndex = -1;
const chipEls = {};
catOrder.forEach(cat => {
  catOff[cat] = false;
  const chip = document.createElement('button');
  chip.className = 'chip';
  chip.innerHTML = '<span class="dot" style="background:var('+categories[cat].var+')"></span>' + categories[cat].label;
  chip.addEventListener('click', () => {
    exitSolo();
    catOff[cat] = !catOff[cat];
    syncUI();
    render();
  });
  chipsEl.appendChild(chip);
  chipEls[cat] = chip;
});
document.getElementById('count').textContent = allNodes.length + ' documents · ' + allEdges.length + ' connections';

function syncUI(){
  catOrder.forEach(c => chipEls[c].classList.toggle('off', catOff[c]));
  layerBtnEls.forEach(({btn, layer}) => {
    const active = soloIndex === -1 && catOrder.every(c => (categories[c].layer === layer) === !catOff[c])
      && catOrder.some(c => categories[c].layer === layer);
    btn.classList.toggle('active', layer !== 'all' ? active : catOrder.every(c => !catOff[c]) && soloIndex === -1);
  });
  repoBtnEls.forEach(({btn, slug}) => {
    const active = slug === null
      ? Object.values(repoOff).every(v => !v)
      : (!repoOff[slug] && Object.keys(repos).filter(s => s !== slug).every(s => repoOff[s]));
    btn.classList.toggle('active', active);
  });
}

// ---------- layer presets (e.g. Frontend / Backend / Docs / Config) ----------
const layerGroupEl = document.getElementById('layerGroup');
const LAYER_LABELS = {all: 'All', frontend: 'Frontend', backend: 'Backend', docs: 'Docs', config: 'Config'};
const layersPresent = ['all', ...new Set(catOrder.map(c => categories[c].layer).filter(Boolean))];
const layerBtnEls = [];
layersPresent.forEach(layer => {
  const btn = document.createElement('button');
  btn.className = 'layer-btn';
  btn.textContent = LAYER_LABELS[layer] || layer;
  btn.addEventListener('click', () => {
    exitSolo();
    catOrder.forEach(c => { catOff[c] = layer === 'all' ? false : categories[c].layer !== layer; });
    syncUI();
    render();
  });
  layerGroupEl.appendChild(btn);
  layerBtnEls.push({btn, layer});
});
syncUI();

// ---------- tests toggle ----------
let hideTests = false;
const TEST_RE = /\.test\.tsx?$/i;
function isTestNode(d){ return TEST_RE.test(d.file || ''); }
const testsToggleEl = document.getElementById('testsToggle');
testsToggleEl.addEventListener('click', () => {
  hideTests = !hideTests;
  testsToggleEl.classList.toggle('active', hideTests);
  render();
});

// ---------- solo stepper: step through one category at a time ----------
const stepLabelEl = document.getElementById('stepLabel');
function applySolo(){
  const cat = catOrder[soloIndex];
  catOrder.forEach(c => { catOff[c] = c !== cat; });
  stepLabelEl.textContent = (soloIndex + 1) + ' / ' + catOrder.length + ': ' + categories[cat].label;
  stepLabelEl.classList.add('active');
  syncUI();
  render();
}
function exitSolo(){
  if (soloIndex === -1) return;
  soloIndex = -1;
  stepLabelEl.textContent = 'Step through';
  stepLabelEl.classList.remove('active');
}
document.getElementById('stepNext').addEventListener('click', () => {
  soloIndex = soloIndex === -1 ? 0 : (soloIndex + 1) % catOrder.length;
  applySolo();
});
document.getElementById('stepPrev').addEventListener('click', () => {
  soloIndex = soloIndex === -1 ? catOrder.length - 1 : (soloIndex - 1 + catOrder.length) % catOrder.length;
  applySolo();
});
document.getElementById('stepExit').addEventListener('click', () => {
  exitSolo();
  catOrder.forEach(c => { catOff[c] = false; });
  syncUI();
  render();
});

const panelEmptyDefault =
  '<h2>' + allNodes.length + ' documents, ' + allEdges.length + ' connections</h2>' +
  '<p>Click a node for its full title, file path, summary, and every connection it has. A dashed border is a cluster — a button in the panel opens the real files inside it.</p>' +
  '<dl>' + catOrder.map(c => '<dt>' + categories[c].label + '</dt><dd>' + (categories[c].hint||'') + '</dd>').join('') + '</dl>';

// ---------- svg setup ----------
const wrap = document.getElementById('graph-wrap');
const svg = d3.select('#svg');
const gZoom = svg.append('g').attr('class', 'zoom-layer');
const gEdges = gZoom.append('g').attr('class', 'edges-layer');
const gNodes = gZoom.append('g').attr('class', 'nodes-layer');

let W = 1800, H = 1150;

function catAnchor(cat){
  const i = catOrder.indexOf(cat);
  const angle = (i / catOrder.length) * Math.PI * 2 - Math.PI/2;
  const R = Math.min(W, H) * 0.36;
  return {x: W/2 + R * Math.cos(angle), y: H/2 + R * Math.sin(angle)};
}

const sim = d3.forceSimulation(nodes)
  .force('link', d3.forceLink(edges).id(d => d.id).distance(65).strength(0.25))
  .force('charge', d3.forceManyBody().strength(-130))
  .force('collide', d3.forceCollide(54))
  .force('x', d3.forceX(d => catAnchor(d.cat).x).strength(0.14))
  .force('y', d3.forceY(d => catAnchor(d.cat).y).strength(0.14))
  .alphaDecay(0.045)
  .alphaMin(0.006)
  .velocityDecay(0.55);

const CARD_W = 148, CARD_H = 40, STRIPE_W = 4;

const edgeSel = gEdges.selectAll('path.edge-line')
  .data(edges)
  .join('path')
  .attr('class', d => 'edge-line' + (d.contra ? ' contra' : ''))
  .attr('marker-end', d => d.contra ? 'url(#arrow-contra)' : 'url(#arrow)');

const edgeLabelSel = gEdges.selectAll('text.edge-label')
  .data(edges)
  .join('text')
  .attr('class', 'edge-label')
  .attr('text-anchor', 'middle')
  .text(d => d.rel);

const defs = svg.append('defs');
[['arrow','var(--edge)'], ['arrow-contra','var(--edge-contra)']].forEach(([id,color]) => {
  defs.append('marker')
    .attr('id', id).attr('viewBox', '0 0 10 10')
    .attr('refX', 9).attr('refY', 5)
    .attr('markerWidth', 6.5).attr('markerHeight', 6.5)
    .attr('orient', 'auto-start-reverse')
    .append('path').attr('d', 'M0,0 L10,5 L0,10 z').attr('fill', color);
});

const nodeSel = gNodes.selectAll('g.node')
  .data(nodes)
  .join('g')
  .attr('class', d => {
    let c = 'node';
    if (d.type === 'cluster') c += ' is-cluster';
    if (d.cat === 'extern') c += ' is-extern';
    if (d.status === 'superseded') c += ' is-superseded';
    return c;
  })
  .call(d3.drag()
    .on('start', (event,d) => { if(!event.active) sim.alphaTarget(0.15).restart(); d.fx=d.x; d.fy=d.y; })
    .on('drag', (event,d) => { d.fx=event.x; d.fy=event.y; })
    .on('end', (event,d) => { if(!event.active) sim.alphaTarget(0); }));

nodeSel.append('rect').attr('class','card')
  .attr('width', CARD_W).attr('height', CARD_H).attr('x', -CARD_W/2).attr('y', -CARD_H/2).attr('rx', 8);
nodeSel.append('rect').attr('class','stripe')
  .attr('width', STRIPE_W).attr('height', CARD_H).attr('x', -CARD_W/2).attr('y', -CARD_H/2).attr('rx', 2)
  .attr('fill', d => 'var(' + categories[d.cat].var + ')');
nodeSel.append('text').attr('class','label').attr('x', -CARD_W/2 + 11).attr('y', -3)
  .text(d => d.label.length > 24 ? d.label.slice(0,23)+'…' : d.label);
nodeSel.append('text').attr('class','cat-tag').attr('x', -CARD_W/2 + 11).attr('y', 12)
  .attr('fill', d => 'var(' + categories[d.cat].var + ')')
  .text(d => d.type === 'cluster' ? (d.childCount + ' files ↴') : categories[d.cat].label.toUpperCase());
nodeSel.append('title').text(d => d.title);
nodeSel.on('click', (event, d) => { event.stopPropagation(); selectNode(d.id); });
svg.on('click', () => selectNode(null));

sim.on('tick', () => {
  edgeSel.attr('d', d => 'M' + d.source.x + ',' + d.source.y + ' L' + d.target.x + ',' + d.target.y);
  edgeLabelSel.attr('x', d => (d.source.x + d.target.x)/2).attr('y', d => (d.source.y + d.target.y)/2 - 3);
  nodeSel.attr('transform', d => 'translate(' + d.x + ',' + d.y + ')');
});

let fitted = false;
sim.on('end', () => { if(!fitted){ fitted = true; fitToContent(); } });

function fitToContent(){
  const cw = wrap.clientWidth, ch = wrap.clientHeight;
  // A small/simple graph can settle (sim.on('end')) before the flex/grid
  // layout has given #graph-wrap a real size, especially on first paint —
  // fitting against a 0x0 container commits scale(0) forever, since this
  // only runs once (see `fitted` below). Retry instead of committing.
  if (!cw || !ch) { requestAnimationFrame(fitToContent); return; }
  const xs = nodes.map(n=>n.x), ys = nodes.map(n=>n.y);
  const minX = Math.min(...xs)-100, maxX = Math.max(...xs)+100;
  const minY = Math.min(...ys)-80, maxY = Math.max(...ys)+80;
  const bw = maxX-minX, bh = maxY-minY;
  let scale = Math.min(cw/bw, ch/bh, 1.0);
  if (!Number.isFinite(scale) || scale <= 0) scale = 1;
  const tx = cw/2 - scale*(minX+bw/2);
  const ty = ch/2 - scale*(minY+bh/2);
  svg.transition().duration(500).call(zoom.transform, d3.zoomIdentity.translate(tx,ty).scale(scale));
}

const zoom = d3.zoom().scaleExtent([0.15, 3]).on('zoom', (event) => { gZoom.attr('transform', event.transform); });
svg.call(zoom);

function resize(){
  W = wrap.clientWidth || 900;
  H = wrap.clientHeight || 560;
  svg.attr('width', W).attr('height', H).attr('viewBox', '0 0 ' + W + ' ' + H);
  sim.force('x', d3.forceX(d => catAnchor(d.cat).x).strength(0.14));
  sim.force('y', d3.forceY(d => catAnchor(d.cat).y).strength(0.14));
}
window.addEventListener('resize', () => { resize(); });
resize();

// ---------- selection + filtering ----------
const searchInput = document.getElementById('search');
let query = '';
let selectedId = null;
const panel = document.getElementById('panel');

function selectNode(id){
  selectedId = id;
  render();
  if (id){
    renderPanel(nodeById.get(id));
    panel.classList.add('open');
  } else {
    panel.innerHTML = '<div class="empty" id="panel-empty">' + panelEmptyDefault + '</div>';
    panel.classList.remove('open');
  }
}
selectNode(null);

function renderPanel(d){
  const cat = categories[d.cat];
  const inc = incident.get(d.id) || [];
  const rows = inc.map(e => {
    const out = e.from === d.id;
    const otherId = out ? e.to : e.from;
    const other = nodeById.get(otherId);
    return {other, rel: e.rel, out, contra: !!e.contra};
  });
  const isCluster = d.type === 'cluster';
  panel.innerHTML =
    '<button id="close-panel">← Close</button>' +
    '<span class="cat-chip"><span class="dot" style="background:var(' + cat.var + ')"></span>' + cat.label + (isCluster ? ' · Cluster' : '') + '</span>' +
    (d.status ? '<span class="status-tag' + (d.status==='superseded' ? ' superseded' : '') + '">Status: ' + d.status + '</span>' : '') +
    '<h2>' + d.title + '</h2>' +
    '<div class="filepath">' + d.file + '</div>' +
    (d.summary && d.summary !== d.title ? '<p class="summary">' + d.summary + '</p>' : '') +
    (isCluster ? '<button class="drill-open-btn" id="open-drill">→ View all ' + d.childCount + ' files</button>' : '') +
    (d.content ? '<button class="drill-open-btn" id="open-reader">→ View full text (' + d.content.split(/\r?\n/).length + ' lines)</button>' : '') +
    (rows.length ? '<div class="rel-heading">Connections (' + rows.length + ')</div><ul class="rel-list">' +
      rows.map(r => '<li class="rel-item' + (r.contra?' contra':'') + '" data-id="' + r.other.id + '">' +
        '<div class="rel-label">' + (r.out ? '→ ' : '← ') + r.rel + '</div>' +
        '<div class="rel-target">' + r.other.label + '</div></li>').join('') +
      '</ul>' : '');
  panel.querySelectorAll('.rel-item').forEach(el => {
    el.addEventListener('click', () => selectNode(el.getAttribute('data-id')));
  });
  const closeBtn = document.getElementById('close-panel');
  if (closeBtn) closeBtn.addEventListener('click', () => selectNode(null));
  const drillBtn = document.getElementById('open-drill');
  if (drillBtn) drillBtn.addEventListener('click', () => openDrill(d.id));
  const readerBtn = document.getElementById('open-reader');
  if (readerBtn) readerBtn.addEventListener('click', () => openReader(d.id));
}

searchInput.addEventListener('input', () => { query = searchInput.value.trim().toLowerCase(); render(); if(drillOpenId) renderDrillGrid(drillOpenId); });

function matches(d){
  if (!query) return true;
  return (d.label + ' ' + d.title + ' ' + d.file + ' ' + d.summary).toLowerCase().includes(query);
}

function render(){
  const egoSet = selectedId ? neighbors.get(selectedId) : null;
  nodeSel.style('opacity', d => {
    if (selectedId) return (d.id === selectedId || (egoSet && egoSet.has(d.id))) ? 1 : 0.12;
    if (hideTests && isTestNode(d)) return 0.08;
    if (isRepoHidden(d)) return 0.08;
    if (catOff[d.cat]) return 0.12;
    if (!matches(d)) return 0.15;
    return 1;
  });
  nodeSel.classed('selected', d => d.id === selectedId);
  nodeSel.raise();
  edgeSel
    .classed('hi', e => selectedId && (e.source.id === selectedId || e.target.id === selectedId))
    .style('opacity', e => {
      if (selectedId) return (e.source.id === selectedId || e.target.id === selectedId) ? 1 : 0.04;
      const dim = (catOff[e.source.cat] || catOff[e.target.cat])
        || (hideTests && (isTestNode(e.source) || isTestNode(e.target)))
        || (isRepoHidden(e.source) || isRepoHidden(e.target))
        || (query && (!matches(e.source) && !matches(e.target)));
      return dim ? 0.04 : 0.22;
    });
  edgeLabelSel.style('opacity', e => {
    // Dense import graphs (100s of edges) are unreadable with every label
    // shown at rest; labels appear only for the selected node's own edges.
    if (selectedId) return (e.source.id === selectedId || e.target.id === selectedId) ? 1 : 0;
    return 0;
  });
}
render();

// ---------- drill-down ----------
let drillOpenId = null;
const drillEl = document.getElementById('drill');
document.getElementById('drill-back').addEventListener('click', closeDrill);

function openDrill(clusterId){
  drillOpenId = clusterId;
  const d = nodeById.get(clusterId);
  document.getElementById('drill-title').textContent = d.title;
  document.getElementById('drill-desc').textContent = d.summary;
  renderDrillGrid(clusterId);
  drillEl.classList.add('open');
  panel.classList.remove('open');
}
function closeDrill(){ drillOpenId = null; drillEl.classList.remove('open'); }

function renderDrillGrid(clusterId){
  const kids = (childrenOf.get(clusterId) || []).slice().sort((a,b) => a.title.localeCompare(b.title));
  const grid = document.getElementById('drill-grid');
  grid.innerHTML = kids.map(k => {
    const cat = categories[k.cat];
    const dim = query && !matches(k);
    return '<div class="drill-card' + (dim?' is-dim':'') + '" data-id="' + k.id + '">' +
      '<div class="stripe" style="background:var(' + cat.var + ')"></div>' +
      '<div class="t">' + k.title + '</div>' +
      '<div class="f">' + k.file + '</div>' +
      (k.summary && k.summary !== k.title ? '<div class="s">' + k.summary + '</div>' : '') +
      '</div>';
  }).join('');
  grid.querySelectorAll('.drill-card').forEach(el => {
    el.addEventListener('click', () => selectNode(el.getAttribute('data-id')));
  });
}

// ---------- full-text reader ----------
const readerEl = document.getElementById('reader');
document.getElementById('reader-back').addEventListener('click', closeReader);

function escapeHtml(s){
  return s.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;');
}
function openReader(nodeId){
  const d = nodeById.get(nodeId);
  document.getElementById('reader-title').textContent = d.title;
  document.getElementById('reader-desc').textContent = d.file;
  const isMd = /\.md$/i.test(d.file);
  document.getElementById('reader-md').innerHTML = isMd
    ? marked.parse(d.content || '')
    : '<pre><code>' + escapeHtml(d.content || '') + '</code></pre>';
  readerEl.classList.add('open');
  panel.classList.remove('open');
}
function closeReader(){ readerEl.classList.remove('open'); }

document.getElementById('reset').addEventListener('click', () => {
  exitSolo();
  catOrder.forEach(c => catOff[c] = false);
  if (repos) Object.keys(repos).forEach(s => { repoOff[s] = false; });
  hideTests = false;
  testsToggleEl.classList.remove('active');
  syncUI();
  query = ''; searchInput.value = '';
  closeDrill();
  closeReader();
  selectNode(null);
  nodes.forEach(n => { n.fx = null; n.fy = null; });
  sim.alpha(0.9).restart();
  fitted = false;
});

})();
</script>
```

## `build_viewer.py`

Embeds a `graph.json` sitting next to it into `viewer.template.html`,
producing `viewer.html`. Run this after every `build_graph.py` run.

```python
#!/usr/bin/env python3
"""Regenerates viewer.html by embedding graph.json into viewer.template.html.
Run after editing build_graph.py + regenerating graph.json, or after editing the template."""
import json, os

HERE = os.path.dirname(os.path.abspath(__file__))

with open(os.path.join(HERE, "graph.json"), encoding="utf-8") as f:
    data = json.load(f)

with open(os.path.join(HERE, "viewer.template.html"), encoding="utf-8") as f:
    template = f.read()

payload = json.dumps(data, ensure_ascii=False).replace("</", "<\\/")
start_marker = "/*__GRAPH_DATA__*/{}/*__END_GRAPH_DATA__*/"
if start_marker not in template:
    raise SystemExit("placeholder not found in template")
fragment = template.replace(start_marker, payload)

# A raw JSON string embedded in a <script> can contain a literal "</script"
# substring (e.g. a code file whose content mentions one in a comment or
# example) — the .replace("</", "<\\/") above is what stops that from
# terminating the tag early and corrupting the rest of the page. Do not
# remove it.

fragment_path = os.path.join(HERE, "viewer.fragment.html")
with open(fragment_path, "w", encoding="utf-8") as f:
    f.write(fragment)

# Build a fully standalone page too: <!DOCTYPE>/<html>/<head> with an
# explicit UTF-8 charset. Python's http.server sends no charset header, and
# without <meta charset> a browser can mis-decode UTF-8 punctuation (em
# dashes, curly quotes) as Latin-1 — this avoids that regardless of how the
# file ends up served.
title_end = fragment.index("</style>") + len("</style>")
head_part = fragment[:title_end]
body_part = fragment[title_end:]
standalone = (
    "<!DOCTYPE html>\n<html lang=\"en\">\n<head>\n"
    "<meta charset=\"utf-8\">\n"
    "<meta name=\"viewport\" content=\"width=device-width, initial-scale=1\">\n"
    + head_part +
    "\n</head>\n<body>\n" + body_part + "\n</body>\n</html>\n"
)

standalone_path = os.path.join(HERE, "viewer.html")
with open(standalone_path, "w", encoding="utf-8") as f:
    f.write(standalone)

print("written", fragment_path, "-", len(fragment), "bytes")
print("written", standalone_path, "-", len(standalone), "bytes (open this one directly)")
```

## `build_graph.py` — starter template, adapt every `# TODO`

This is the one file that is genuinely different per repo. What follows is
a small, complete, *runnable* starting point — a fictional repo with two
docs, one ADR, a TypeScript server/client split, and one illustrative
cluster — not a generic tool. Copy it in and replace the TODOs with the
target repo's real content and real folder layout.

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""TEMPLATE — copy this into the target repo (e.g. as tools/knowledge-graph/
build_graph.py) and adapt every section marked TODO. This is a *starter*,
not a drop-in tool: `doc_nodes`/`doc_edges` are illustrative (a fictional
architecture.md + ADR), `category_for()`'s folder rules are illustrative
(a generic src/server + src/client split), and the regex-based import
extraction is written for TypeScript — swap the regex/resolution for
whatever language the target repo's source track actually uses.

Two extraction tracks, deliberately different in *how much is read*:
- doc_nodes / doc_edges: every file was actually read in full — real
  one/two-sentence summaries, real cross-reference edges you can point to a
  sentence for. Collapse a large uniform group of files (e.g. 40
  near-identical drafts, 25 generic reference pages) into one `type:
  "cluster"` node with real `children` entries rather than 40 individual
  summaries — see `clusters_raw` below.
- everything under "structural extraction": nothing is read for meaning.
  Real import/require statements, resolved with the language's own
  module-resolution rules, kept only when they resolve to another real file
  in the same tree. An import of an external package is not an edge.
"""
import json
import os
import re

ROOT = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))  # TODO: point at the target repo root

categories = {
    # TODO: one entry per category this repo actually has. "layer" is
    # optional — only add it to categories that share a real architectural
    # split (e.g. frontend vs. backend); leave it off entirely if the repo
    # doesn't have one, and the layer-switcher row simply won't render.
    "docs":   {"label": "Docs",   "hint": "docs/*.md"},
    "adr":    {"label": "ADR",    "hint": "docs/adr/*.md"},
    "server": {"label": "Server", "hint": "src/server/**", "layer": "backend"},
    "client": {"label": "Client", "hint": "src/client/**", "layer": "frontend"},
}

# ---------------------------------------------------------------------------
# 1. Doc track — hand-curated. (id=path, title, summary)
# TODO: replace with every doc/ADR file actually read for this repo.
# ---------------------------------------------------------------------------
doc_nodes = [
    ("docs/architecture.md", "Architecture overview",
     "TODO: what this file actually says, in one or two real sentences."),
    ("docs/adr/0001-example-decision.md", "ADR 0001: Example decision",
     "TODO: the real decision and its real reasoning, from actually reading the file."),
]

doc_edges = [
    # (from_id, to_id, relationship, contra) — contra=True renders dashed,
    # reserve it for genuine "X marks Y outdated/replaced" relationships.
    ("docs/adr/0001-example-decision.md", "docs/architecture.md", "precedes", False),
    ("docs/architecture.md", "src/server/api.ts", "implemented in", False),
]

# Large uniform groups collapse into one cluster node with real children.
# (cluster_id, label, title, cat, base_dir, summary, children, parent_node_or_None)
# children: list of (filename_without_ext, title)
clusters_raw = [
    # TODO: only add a cluster for a genuinely large, uniform group (dozens
    # of near-identical files). Omit this list entirely if the repo has none.
    (
        "content_drafts", "Example Drafts (N)", "N example draft files", "docs",
        "docs/drafts/",
        "N near-identical files, collapsed into one cluster node — see the drill-down "
        "grid in the viewer instead of N individual top-level nodes.",
        [
            ("draft-one", "Draft One — TODO real title"),
            ("draft-two", "Draft Two — TODO real title"),
        ],
        None,
    ),
]

# ---------------------------------------------------------------------------
# 2. Code track — structural extraction. TypeScript shown; adapt per language.
# ---------------------------------------------------------------------------
TS_EXT = (".ts", ".tsx")


def category_for(rel_path: str) -> str:
    # TODO: real folder → category rules for this repo.
    p = rel_path.replace(os.sep, "/")
    if p.startswith("docs/adr/"):
        return "adr"
    if p.startswith("docs/"):
        return "docs"
    if p.startswith("src/server/"):
        return "server"
    if p.startswith("src/client/"):
        return "client"
    return "docs"


IMPORT_RE = re.compile(r"""(?:from|import)\s*\(?\s*['"]([^'"]+)['"]""")


def find_ts_files():
    out = []
    for base in ("src",):  # TODO: real source roots
        base_path = os.path.join(ROOT, base)
        if not os.path.isdir(base_path):
            continue
        for dirpath, _dirs, files in os.walk(base_path):
            for f in files:
                if f.endswith(TS_EXT):
                    full = os.path.join(dirpath, f)
                    out.append(os.path.relpath(full, ROOT).replace(os.sep, "/"))
    return sorted(out)


def resolve_ts_import(importer_rel: str, spec: str, all_ts: set):
    # TODO: swap "~/" for whatever path alias (if any) this repo's
    # tsconfig.json declares, or drop this branch if there is none.
    if spec.startswith("~/"):
        candidate_base = "src/" + spec[2:]
    elif spec.startswith("."):
        importer_dir = os.path.dirname(importer_rel)
        candidate_base = os.path.normpath(os.path.join(importer_dir, spec)).replace(os.sep, "/")
    else:
        return None  # external package — not an edge
    for suffix in ("", ".ts", ".tsx", "/index.ts", "/index.tsx"):
        cand = candidate_base + suffix
        if cand in all_ts:
            return cand
    return None


# For a Python source tree instead/as well, the same pattern is:
#   PY_IMPORT_RE = re.compile(r"^\s*(?:from\s+([\w\.]+)\s+import|import\s+([\w\.]+))", re.MULTILINE)
# then resolve a dotted module path against the package's real directory layout.


def build():
    nodes = []
    node_ids = set()

    for path, title, summary in doc_nodes:
        nodes.append({"id": path, "label": title, "title": title,
                       "cat": category_for(path), "file": path, "summary": summary,
                       "type": "doc"})
        node_ids.add(path)

    for (cid, label, title, cat, basedir, summary, items, parent) in clusters_raw:
        cnode = {"id": cid, "label": label, "title": title, "cat": cat,
                 "file": basedir + f" ({len(items)} files)", "summary": summary,
                 "type": "cluster", "childCount": len(items)}
        if parent:
            cnode["clusterParentNode"] = parent
        nodes.append(cnode)
        node_ids.add(cid)
        for i, (fname, ctitle) in enumerate(items):
            child_id = f"{cid}__{i}"
            nodes.append({
                "id": child_id, "label": ctitle[:60], "title": ctitle, "cat": cat,
                "file": basedir + fname + ".md", "summary": ctitle,
                "type": "doc", "parent": cid,
            })
            node_ids.add(child_id)

    # -- structural TS nodes + import edges --
    ts_files = find_ts_files()
    ts_set = set(ts_files)
    edges = []
    import_counts = {}
    for f in ts_files:
        if f in node_ids:
            continue
        nodes.append({"id": f, "label": os.path.basename(f), "title": f,
                       "cat": category_for(f), "file": f, "summary": "", "type": "doc"})
        node_ids.add(f)

    for f in ts_files:
        full = os.path.join(ROOT, f)
        try:
            with open(full, encoding="utf-8", errors="replace") as fh:
                text = fh.read()
        except OSError:
            continue
        seen_targets = set()
        for spec in IMPORT_RE.findall(text):
            target = resolve_ts_import(f, spec, ts_set)
            if target and target != f and target not in seen_targets:
                edges.append({"from": f, "to": target, "rel": "imports"})
                seen_targets.add(target)
        import_counts[f] = len(seen_targets)

    for n in nodes:
        if n.get("summary") == "" and n["id"] in import_counts:
            c = import_counts[n["id"]]
            n["summary"] = (f"imports {c} internal module(s)") if c else "imports no internal modules"

    for (f, t, rel, contra) in doc_edges:
        if f in node_ids and t in node_ids:
            e = {"from": f, "to": t, "rel": rel}
            if contra:
                e["contra"] = True
            edges.append(e)
        else:
            print("WARNING - doc edge references missing node(s):",
                  [x for x in (f, t) if x not in node_ids])
    for (cid, _l, _t, _c, _b, _s, _items, parent) in clusters_raw:
        if parent:
            edges.append({"from": parent, "to": cid, "rel": "contains"})

    # -- embed full content for every node whose real file exists --
    missing_files = []
    for n in nodes:
        fpath = os.path.join(ROOT, n["file"])
        if os.path.isfile(fpath):
            with open(fpath, encoding="utf-8", errors="replace") as fh:
                n["content"] = fh.read()
        else:
            missing_files.append(n["file"])
    if missing_files:
        print("WARNING - files not found on disk, no content embedded:")
        for m in missing_files:
            print("  -", m)

    return {
        "generated": "TODO-YYYY-MM-DD",
        "source_repo": "TODO-repo-name",
        "methodology": (
            "TODO: one sentence on the two extraction tracks used for this repo, "
            "matching what doc_nodes/doc_edges and the structural extraction above "
            "actually did."
        ),
        "categories": categories,
        "nodes": nodes,
        "edges": edges,
    }


if __name__ == "__main__":
    data = build()
    out_path = os.path.join(os.path.dirname(os.path.abspath(__file__)), "graph.json")
    with open(out_path, "w", encoding="utf-8") as f:
        json.dump(data, f, ensure_ascii=False, indent=1)

    node_ids = set(n["id"] for n in data["nodes"])
    missing = set()
    for e in data["edges"]:
        if e["from"] not in node_ids:
            missing.add(e["from"])
        if e["to"] not in node_ids:
            missing.add(e["to"])
    dupes_check = [n["id"] for n in data["nodes"]]
    dupes = set(x for x in dupes_check if dupes_check.count(x) > 1)

    print("nodes:", len(data["nodes"]))
    print("edges:", len(data["edges"]))
    print("missing node ids referenced by edges:", missing if missing else "NONE - OK")
    print("duplicate ids:", dupes if dupes else "NONE - OK")
    print("written to", out_path)
```

## `merge_repos.py` — for cross-repo mode only

Only needed when merging two or more already-built `graph.json` files. Fill
in `REPOS` and (optionally) `CROSS_REPO_EDGES` for the real repos.

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""Merges N single-repo graph.json files (each built by this skill's per-repo
build_graph.py) into one cross-repo graph.json.

Usage: edit REPOS and CROSS_REPO_EDGES below for your repos, then run.

What it does, mechanically:
- Every node id and every category key gets namespaced with "<slug>::" /
  "<slug>__" so two repos' "docs" categories, or a coincidentally identical
  file path, never collide.
- Every node gets a `repo: <slug>` field, which the viewer uses to render an
  extra "All repos / <repo> / <repo>" filter row above the category chips
  (only when more than one repo is present — a single-repo graph.json is
  untouched by this and looks exactly as before).
- Cross-repo edges are NOT auto-detected (there is usually no `import`
  statement across two separate repos to regex for) — list the real ones you
  know about by hand in CROSS_REPO_EDGES, using the *un-prefixed* ids from
  each source graph.json; this script prefixes them for you. Add exactly
  the ones you can verify — a cross-repo graph with zero verified edges is
  still a legitimate output; it just means the repos are genuinely
  independent, which is itself the finding.
"""
import json
import os

HERE = os.path.dirname(os.path.abspath(__file__))

# (slug, display label, path to that repo's graph.json)
REPOS = [
    ("repo-a", "repo-a", "/path/to/repo-a/knowledge-graph/graph.json"),
    ("repo-b", "repo-b", "/path/to/repo-b/knowledge-graph/graph.json"),
]

# Real, hand-verified links between the repos.
# (from_slug, from_id, to_slug, to_id, rel) — ids as they appear in the
# SOURCE graph.json, before this script prefixes them.
CROSS_REPO_EDGES = [
    # ("repo-a", "docs/some-doc.md", "repo-b", "docs/other-doc.md", "references"),
]

OUT_DIR = os.path.join(HERE, "cross-repo-output")


def load(path):
    with open(path, encoding="utf-8") as f:
        return json.load(f)


def prefix_node_id(slug, node_id):
    return f"{slug}::{node_id}"


def prefix_cat_key(slug, cat_key):
    return f"{slug}__{cat_key}"


def build():
    merged_categories = {}
    merged_nodes = []
    merged_edges = []
    repos_meta = {}

    for slug, label, path in REPOS:
        data = load(path)
        repos_meta[slug] = {"label": label, "source_repo": data.get("source_repo", slug)}

        cat_key_map = {}
        for cat_key, cat_val in data["categories"].items():
            new_key = prefix_cat_key(slug, cat_key)
            cat_key_map[cat_key] = new_key
            merged_categories[new_key] = dict(cat_val)  # copy, keep label/hint/layer as-is

        for n in data["nodes"]:
            n2 = dict(n)
            n2["id"] = prefix_node_id(slug, n["id"])
            n2["cat"] = cat_key_map[n["cat"]]
            n2["repo"] = slug
            if "parent" in n2:
                n2["parent"] = prefix_node_id(slug, n["parent"])
            if "clusterParentNode" in n2:
                n2["clusterParentNode"] = prefix_node_id(slug, n["clusterParentNode"])
            merged_nodes.append(n2)

        for e in data["edges"]:
            e2 = dict(e)
            e2["from"] = prefix_node_id(slug, e["from"])
            e2["to"] = prefix_node_id(slug, e["to"])
            merged_edges.append(e2)

    node_ids = set(n["id"] for n in merged_nodes)
    for from_slug, from_id, to_slug, to_id, rel in CROSS_REPO_EDGES:
        f = prefix_node_id(from_slug, from_id)
        t = prefix_node_id(to_slug, to_id)
        if f not in node_ids or t not in node_ids:
            missing = [x for x in (f, t) if x not in node_ids]
            print("WARNING - cross-repo edge references missing node(s):", missing)
            continue
        merged_edges.append({"from": f, "to": t, "rel": rel, "crossRepo": True})

    methodology = (
        "Cross-repo graph: " + " + ".join(r["label"] for r in repos_meta.values())
        + f". {len(REPOS)} independently built knowledge graphs merged (node ids and "
        "categories namespaced per repo), plus " + str(len(CROSS_REPO_EDGES)) +
        " hand-verified edge(s) between the repos — cross-repo edges are never "
        "auto-guessed, only entered once actually confirmed."
    )

    data = {
        "generated": max(load(p)["generated"] for _, _, p in REPOS),
        "source_repo": " + ".join(r["label"] for r in repos_meta.values()),
        "methodology": methodology,
        "repos": repos_meta,
        "categories": merged_categories,
        "nodes": merged_nodes,
        "edges": merged_edges,
    }
    return data


if __name__ == "__main__":
    data = build()
    os.makedirs(OUT_DIR, exist_ok=True)
    out_path = os.path.join(OUT_DIR, "graph.json")
    with open(out_path, "w", encoding="utf-8") as f:
        json.dump(data, f, ensure_ascii=False, indent=1)

    node_ids = set(n["id"] for n in data["nodes"])
    missing = set()
    for e in data["edges"]:
        if e["from"] not in node_ids:
            missing.add(e["from"])
        if e["to"] not in node_ids:
            missing.add(e["to"])
    dupes_check = [n["id"] for n in data["nodes"]]
    dupes = set(x for x in dupes_check if dupes_check.count(x) > 1)

    print("repos:", list(data["repos"].keys()))
    print("nodes:", len(data["nodes"]))
    print("edges:", len(data["edges"]), "(incl.", sum(1 for e in data["edges"] if e.get("crossRepo")), "cross-repo)")
    print("missing node ids referenced by edges:", missing if missing else "NONE - OK")
    print("duplicate ids:", dupes if dupes else "NONE - OK")
    print("written to", out_path)
    print()
    print("Next: copy viewer.template.html and build_viewer.py next to", out_path,
          "and run build_viewer.py there.")
```
