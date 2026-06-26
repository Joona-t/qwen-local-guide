# Qwen Local — Mac mini M4 build companion

An interactive, **single self-contained HTML guide** for running Qwen locally on a base
**Mac mini M4 (16 GB)** and building toward a local **RAG + multi-agent shared-memory** stack.
It's the interactive build of the research document in [`source/qwen-local-guide.md`](source/qwen-local-guide.md),
which remains the source of truth for every technical detail.

## Open it

Just open **`index.html`** in any browser — double-click it, or drag it onto a browser window.

- **No server, no build step, no network.** Fonts (OpenDyslexic) and the Sparky mascot are base64-inlined,
  so the file works fully offline and can be moved/emailed anywhere as one artifact.
- Nothing leaves your machine. Build progress, hardening checklist, and theme choice are saved in
  `localStorage` (this browser only), with a graceful in-memory fallback if storage is blocked.

## What's in it

- **Staged build path 0 → 6**, mirroring the doc: inference fundamentals → install Ollama → embeddings →
  RAG → Qdrant → MCP shared memory → security hardening & three-tier routing.
- **A build-progress tracker** + **stage-gate timeline** (week-by-week, with deliverables and thresholds).
- **Three live calculators**, all wired to the doc's own math (and carrying its hedges):
  - **Decode speed** — `tok/s ≈ bandwidth ÷ model size` (120 ÷ 6.6 ≈ ~18).
  - **16 GB "will it fit?" budget** — uses the doc's *stated* resident sizes + the ~1–2 GB KV/runtime band
    against the ~9–12 GB usable window (no fabricated KV megabytes — the doc gives no per-token formula).
  - **Compounding reliability** — `success^steps` (0.95⁸ ≈ ~66 %), the case for keeping multi-step work on Claude.
- **The BFCL capability-cliff chart**, an interactive **three-tier routing diagram**, the **Ollama CVE record**,
  a **hardening checklist**, a **commands cheat-sheet**, and **glossary tooltips**.
- **Every caveat preserved** — all five formal caveats get a first-class section, and inline gotchas/tensions
  sit next to the relevant numbers. Estimates are always labelled as estimates.
- **A "SOTA update — what changed since this guide" section** — the moving frontier as of mid-2026, in seven
  subsections (A–G): newer-than-Qwen3.5 models that still fit 16 GB (gpt-oss-20b, Gemma 4, Ministral 3,
  Granite 4.0); the honest MLX-vs-llama.cpp tradeoff + speculative decoding; quantization past Q4_K_M (Unsloth
  Dynamic, IQ4_XS, MLX DWQ, the thinking-mode quality cliff); switching the embedder default to
  Qwen3-Embedding-0.6B and **adding a reranker**; the hybrid → rerank → Contextual-Retrieval RAG stack; the
  2026 agentic-memory landscape; and a refreshed security section (the GGUF-parser CVE class, OWASP ASI06,
  MINJA, and local prompt-injection defenses like CaMeL + Qwen3Guard). Every figure was multi-agent-researched
  and adversarially fact-checked against primary sources; estimates stay labelled.

## Accessibility

OpenDyslexic body font (system-rounded fallback). The look is the LoveSpark **"candy glass-sticker"**
aesthetic translated from the actual Tongue and Sparky apps (`Theme.swift` / `Colors.swift`): a bubblegum→peach
gradient, glass cards that float on soft pink-halo shadows with embedded film grain + a top gloss, gradient-text
display headings, sticker pills/buttons, the mascot as a glowing bubble with a floating heart, and deep-plum
"terminal" code blocks. Four themes map 1:1 to real app themes — **Candy (Tongue, default)**, **Retro Pink**,
**Dark Noir**, **Beige Paper**. WCAG 2.1 AA is verified across all four (body text 11–15:1; pink is reserved for
fills, never small readable text). Full keyboard navigation, `:focus-visible` rings, `prefers-reduced-motion`
(gates every animation), ARIA roles/labels on charts/calculators/controls, and a print stylesheet. Responsive to 320 px.

## Regenerating

`index.html` is hand-authored with the assets injected at build time. To rebuild from scratch you'd
re-author the markup and re-run the base64 inlining of:

- `…/lib/fonts/opendyslexic-{regular,bold}.woff2` (OpenDyslexic)
- `Claude x LoveSpark/assets/mascot.png` (Sparky)

The technical content is dated **mid-2026** (matching the source). Fast-moving facts (Ollama version pins,
CVEs, Mem0/MCP transport churn, Qwen tags) live as plain editable content, not baked into the JS — edit the
HTML directly to update them.

100% local · no paid API · MIT · made for neurodivergent builders 🐺
