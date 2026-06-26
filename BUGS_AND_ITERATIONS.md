# Bugs & Iterations — Qwen Local build companion

Running log of defects found and fixes landed while building `index.html` from
`source/qwen-local-guide.md`. Newest first.

---

## ITER-005 — SOTA research expansion: "the moving frontier" section (2026-06-26)
- **What:** Repo made public, and a new **"SOTA Update — the local-LLM landscape as of mid-2026"** section
  added to both `source/qwen-local-guide.md` (source of truth) and `index.html` (interactive build). Seven
  subsections A–G covering what shipped/changed since the original core: **A models** (gpt-oss-20b, Gemma 4,
  Ministral-3, Granite 4.0; why `qwen3.5:4b/9b` is still the right pick — 3.6/3.7 have no small-dense tier;
  best tool-caller vs best coder), **B engines** (Ollama v0.30.10, the honest MLX-vs-llama.cpp tradeoff on
  16GB, speculative decoding, engine matrix, vLLM-on-Apple-Silicon), **C quantization** (Unsloth Dynamic,
  IQ4_XS, MLX DWQ, asymmetric KV, QAT/BitNet, the thinking-mode quality cliff), **D embeddings & rerankers**
  (switch default to Qwen3-Embedding-0.6B; add a reranker — the guide had none), **E RAG** (hybrid+rerank+
  Contextual Retrieval as the new default; graph/agentic/ColPali escalations; RAGAS), **F agentic memory**
  (four camps, "your local model is the bottleneck", sleep-time compute, benchmark-saturation skepticism,
  MCP transport), **G security** (refreshed CVEs, the GGUF-parser attack class, poisoned chat templates,
  Qdrant CVE-2026-25628, OWASP ASI06, MINJA, CaMeL + Qwen3Guard defenses).
- **Method:** 7-dimension multi-agent research Workflow (one finder + one adversarial skeptic per dimension,
  14 agents, ~1.06M tokens), each load-bearing claim re-verified via live web search against primary sources.
  All skeptic corrections applied before writing (e.g. Ministral-3 is Dec 2025 not Jan 2026; gpt-oss-20b GPQA
  is 66.0/71.5 not 58.59; Qwen3.5 ships official GPTQ-Int4 but AWQ is community-only; the MCP SSE "June 30
  cutoff" is Atlassian-specific not spec-wide; Ollama rerank PR #7219 was closed not pending; Qdrant fix is
  ≥1.15.6). Estimates labelled as estimates; unverified figures flagged inline.
- **Verification:** HTML tags balanced (22 div / 7 h3 / 4 spec cards / 8 callouts all matched), section +
  nav link render with zero console errors, reuses only existing house-style CSS classes (kicker/lead/
  callout/ctag/code/specgrid/spec). Headless preview can't screenshot it (viewport-height-0 + reveal-anim
  quirk), so verified structurally via DOM reads.

## ITER-004 — Candy glass-sticker re-skin from the real apps (2026-06-18)
- **What:** ITER-003's dark-plum look was still off — it came from the `sparky-agent` *doc*, not the actual
  apps. Studied how the **Tongue** (`Theme.swift`) and **Sparky** (`Colors.swift`) apps are really built and
  rebuilt the CSS to their genuine **"candy glass-sticker Y2K"** aesthetic. Default theme = **Candy (Tongue)**:
  bubblegum→peach→cream diagonal gradient, glass-sticker cards floating on stacked pink-halo shadows (no hard
  borders) with embedded `feTurbulence` film grain + a white top gloss, gradient-text display headings,
  sticker pills/buttons (gradient fill + white edge stroke + colored halo), the mascot as a glowing bubble
  with a floating heart, deep-plum "terminal" code blocks with traffic-light dots, plum-on-cream body text,
  gentle reduced-motion-gated motion (heart bob, hero sparkles, hover-lift, copy pop).
- **Themes:** 4 mapped 1:1 to real app themes — Candy (Tongue, default), Retro Pink (Sparky light), Dark Noir
  (Sparky dark), Beige Paper (Sparky). Content/JS/widgets/IDs/caveats all preserved (CSS-layer re-skin only).

### BUG-009 — Gradient-title light stop failed AA (2026-06-18)
- **Symptom:** the hero/section gradient-text headings ended on `#ff49a6`/`#ff6fbe`, which measured 2.3–2.9:1
  on the light candy/retro background (fails even the 3:1 large-text floor).
- **Fix:** biased `--ls-grad-text` to deep magenta/rose stops (candy `#c92e72→#b3185c→#d4327a`, retro all-deep)
  — every stop now ≥3.28:1 on the darkest bg; beige uses solid headings; `@supports` solid fallback.

### BUG-010 — White text on the bright-pink fill failed AA (2026-06-18)
- **Symptom:** `.pill.brand`, `.finding .n`, `.timeline .done .node`, `.btn.primary`, and the BFCL `.barfill`
  put white text on the bright `--ls-grad-pill` (#ff6fbe→#e91e8c), only ~3:1.
- **Fix:** routed all white-text fills through a per-theme **deep** `--ls-grad-btn` (candy = Tongue's action
  gradient `#d81b73→#a51259`, white ≥4.86) + `--ls-btn-tx`; reserved the bright `--ls-grad-pill` for
  no-text decoration only (budget-bar model segment). Dark themes use bright fills with DARK `btn-tx`.

### BUG-011 — Token fall-through: light chips in dark/alt themes (2026-06-18)
- **Symptom:** `--ls-cotton` (inline-code chip + mascot) and `--ls-mint` (`.pill.ok`) were defined only in the
  candy `:root`; noir/beige inherited candy's *light* values → white-on-light-pink inline code (1.28:1) and
  light-green-on-light-mint pill (1.40:1) in Noir.
- **Fix:** defined `--ls-cotton`/`--ls-mint` per theme (noir dark, beige cream/sage), hardcoded the mascot
  bubble to a fixed light gradient (so `cotton` is free to vary), darkened beige `ok-ink`. All 4 themes now
  pass: inline code 11.8–14.5:1, ok-pill 5.7–7.7:1.

### Dropped — Sakura Sunset theme (2026-06-18)
- A bright peach→plum radial sunset can't carry readable body text on a bare background (white fails on the
  light peach center, dark fails on the plum edge; Sparky avoids this by keeping content on opaque cards).
  Replaced with **Retro Pink** (Sparky's light theme), which is WCAG-clean and still authentically Sparky.

## ITER-003 — Sparky-aesthetic redesign (2026-06-18)
- **What:** Joona disliked the default look. Restyled the guide to match the **Sparky** product aesthetic
  (referenced `sparky-agent/USER_MANUAL.html` + the Sparky UI): deep-plum radial gradient
  (`#2d1830 → #15101a → #0a060e`), bright/soft pink accents (`#ff7ab8` / `#ffb6dc`), near-white pink-tinted text
  (`#f5e6f0`), glass sidebar with `backdrop-filter` blur, pink-soft display headings, and the **mascot as a
  glowing circular avatar**. **Sparky Dark is now the default theme**; Retro Pink / Beige / Slate are alternates.
- **Token system:** Reworked all four theme blocks; added semantic tokens `--ls-heading`, `--ls-pink-soft`,
  `--ls-btn-tx` (button text — dark on the bright-pink button in Sparky, white in the light themes),
  `--ls-glow-strong`. Buttons/pills/finding-numbers/bar-fills now use `--ls-btn-tx` instead of hardcoded `#fff`.
  Theme menu + JS default updated to `sparky`.
- **WCAG:** Dark-on-pink is far easier than the old light-pink default — Sparky measures AAA across the board:
  body text 13.6, pink-soft heading 10.1, muted 6.4, status inks 7–10, button (dark-on-pink) 7.8, code 12.4.
  Retro/Beige/Slate retain their previously-tuned ink tokens and still pass.
- **Verification:** Hero renders the full Sparky look (glowing circular mascot, plum gradient, pink headings,
  glass verdict cards); all four themes switch + persist; calculators reproduce canonical values; deep-section
  content (CVE cards, BFCL, tier cards, calculators) confirmed rendered + correctly styled via DOM inspection;
  zero console errors. (Note: the preview screenshot tool only captures cleanly at scroll 0 on this ~20k-px page;
  scrolled content was verified via DOM metrics + contrast math, not capture.)

## ITER-002 — Adversarial self-review (2026-06-18)
- **What:** Ran a 4-dimension multi-agent review (accuracy-vs-source · code-snippet fidelity · accessibility ·
  JS correctness), each finding independently re-verified before action.
- **Result:** **accuracy = 0 defects, code-fidelity = 0 defects** — the HTML is faithful to the source on every
  number, hedge, all 5 caveats, all 16 gotchas, all 8 tensions, and every code snippet (verbatim). 4 real
  defects confirmed and fixed below.

### BUG-005 — Timeline "done" node digit unreadable in dark & slate themes (HIGH) (2026-06-18)
- **Symptom:** `.timeline li.done .node{… color:#fff}` — but `--ls-ok-ink` is a *light mint* in dark (#5fe0a0)
  and slate (#5fce8f), so white digits on it measured **1.66:1 / 1.96:1**. Introduced by the BUG-002 ink change.
- **Fix:** Use `color:var(--ls-text-inv)` so the digit flips to the theme's inverse (dark on light mint;
  light on the dark-green retro/beige fill). Now retro 7.66 · dark 10.96 · beige 6.67 · slate 8.50. AA in all four.

### BUG-006 — Touch targets under the 32px minimum (LOW) (2026-06-18)
- **Symptom:** `nav.toc a` ≈29px and `.copy` 30px, below CLAUDE.md UI rule #4 (32×32px).
- **Fix:** `nav.toc a{display:flex; align-items:center; min-height:32px}` and `.copy{min-height:32px}`.

### BUG-007 — Glossary disclosure state not announced to screen readers (LOW) (2026-06-18)
- **Symptom:** `.gloss` triggers (`role="button"`) toggled a popover via `.open` but never updated an ARIA state.
- **Fix:** Set `aria-expanded="false"` on each on init and sync it on click/Enter/Space and on outside-click close.

### BUG-008 — Dead no-op loop in the stage-progress callback (LOW) (2026-06-18)
- **Symptom:** A leftover `stageCtl && stageCtl.boxes.forEach(... return)` loop with no side effect (and `stageCtl`
  undefined on first run). The real timeline-lighting is done by the following `timelineNodes.forEach`.
- **Fix:** Deleted the dead block. Re-verified timeline nodes still light from checkbox state; zero console errors.

## ITER-001 — Initial build (2026-06-18)
- **What:** Authored the single-file interactive guide from the research doc: staged 0→6 content,
  build-progress tracker + stage-gate timeline, three live calculators (decode / 16GB budget / compounding
  reliability), BFCL capability-cliff chart, three-tier routing SVG, Ollama CVE cards, hardening checklist,
  commands cheat-sheet, glossary tooltips, four LoveSpark themes, OpenDyslexic + Sparky base64-inlined.
- **Verification:** Served locally; confirmed zero console errors; calculators reproduce the doc's canonical
  values (120÷6.6→~18, 4B→~35, 9B+RAG+short=8.8GB "fits", 13B→15.7GB "exceeds", 0.95⁸→~66%, 9B over 5 steps→~13%);
  theme switching + localStorage persistence across reload; tabs, glossary, checklists, scroll-spy all work;
  responsive 2-col↔1-col verified.

## BUG-001 — Muted text failed AA on the deep end of the pink gradient (2026-06-18)
- **Symptom:** `--ls-text-muted` (#8a3357) on the deepest gradient stop (#f48caf) measured **3.42:1** — below
  the 4.5:1 AA floor for normal text. Affected code captions, timeline thresholds, and TOC links that sit
  directly over the background rather than on a glass card.
- **Root cause:** The retro muted token was tuned for glass surfaces, not the bare gradient.
- **Fix:** Darkened retro `--ls-text-muted` to **#6e2742** (4.54 on the deep stop, 7.20 on glass). One token
  change; verified across all four themes (others already passed: dark 8.3, beige 5.5, slate 7.1 on glass).

## BUG-002 — Vivid accent colors used as small label TEXT failed AA on light themes (2026-06-18)
- **Symptom:** Kicker eyebrows, timeline week labels, cliffmark, callout tags, CVE badges, the `.pill.ok`
  green, and the calculator verdict lines (`.vok/.vwarn/.vbad`) used vivid accent vars as text. On the retro
  gradient/glass these measured **2.0–4.0:1** (e.g. pink-deep 2.03, ok-green 3.75, bad 4.04). This is the
  classic LoveSpark "pink-on-pink" trap — color-decisions.md flags pink-accent as decoration-only.
- **Root cause:** Reused decorative accent tokens for readable text on light backgrounds.
- **Fix:** Introduced darkened "ink" tokens (`--ls-ok-ink`, `--ls-warn-ink`, `--ls-bad-ink`, `--ls-label-ink`,
  `--ls-lab-{note,gotcha,security,caveat}`) per theme; vivid accents retained only for borders/dots/fills.
  Verified: label-ink #67203b = 4.99 on deep gradient; lab inks 5.4–6.7 on glass; ok-ink green #0a6038 = 5.33
  on glass / 6.05 on beige. In dark/slate themes the ink tokens map to the (already-passing) vivid accents.

## BUG-003 — Syntax-highlight colors failed AA on the near-transparent code background (2026-06-18)
- **Symptom:** `.code .str/.kw/.flag/.num` (teal/pink/amber/blue) sat on a 6%-tint code background that was
  effectively the light pink page — contrast **2.0–2.5:1**.
- **Root cause:** Code background was a subtle tint rather than a proper editor surface.
- **Fix:** Made the code block a **dark editor panel in every theme** (`--ls-code-bg:#241320`) with bright fixed
  syntax tokens (`--ls-code-{cmt,str,kw,flag,num}`) — all 7–14:1 on the dark panel. Inline `<code>` kept as a
  light glass chip with body-ink text (always passes). White-on-cliff-red bar fixed to #a82a44 (6.82:1).

## BUG-004 — Black gaps on scroll from a viewport-fixed background on a transparent root (2026-06-18)
- **Symptom:** With `background-attachment:fixed` on `body` and a transparent `html`, scrolled regions painted
  black (reproduced in headless capture; a real risk in some browsers).
- **Root cause:** The fixed background only covers the initial paint box; the root element had no background.
- **Fix:** Moved the themeable background to the **root element** with a **solid theme-colored fallback behind
  the gradient** (`html{ background-image:var(--ls-bg-gradient); background-attachment:fixed; background-color:var(--ls-bg-mid) }`),
  moved the `data-theme` attribute from `body` to `:root` (so theme switching still drives the root background),
  and set `body{background:transparent}`. No more gaps in any theme; beige/slate (solid backgrounds) handled by
  the color fallback. Verified across all four themes.
