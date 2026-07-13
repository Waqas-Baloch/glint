# Glint Context Ranking Specification — implementation notes

Glint's selector (`src/core/ranking/`) implements the Context Ranking
Specification: task-aware unit classification, anchor-seeded graph
expansion with distance decay, a 9-signal weighted score, and tiered
budget packing. This doc records the exact formulas and — since a
local-first CLI has no embeddings, no ML classifier, and no PR-system
integration — exactly where a concrete, deterministic proxy stands in
for a signal the spec names but Glint can't compute directly.

## Practical unit granularity

The spec defines four unit levels (Surface/Component/Symbol/Property).
Glint ranks at **file granularity** — building a true symbol-level
dependency graph (cross-referencing individual functions/calls, not just
file imports) would need something close to a language server. Instead,
each file is classified into the level it most precisely represents:

- **Symbol** — the file exports a declaration matching one of the task's
  anchor hints (ts-morph `getExportedDeclarations()`).
- **Property** — the file defines a matching DOM id/class/heading (HTML/CSS)
  or a matching JSX attribute value like `label="Checkout"` (TS/TSX/JS/JSX,
  narrowed to `identifier="value"` syntax specifically — a bare quoted-string
  scan matches array/object literal data, e.g. a keyword list containing the
  word `"margin"`, as false positives).
- **Surface** — the path matches a route/page/endpoint/worker/job/entry
  pattern (`app/**/page.tsx`, `pages/api/**`, `**/*.route.ts`, …).
- **Component** — the default bucket for everything else.

## Two-stage pipeline (`rank.ts`)

1. **Stage A** (`taskProfile.ts`) — classify task type + scope from the
   task text via keyword buckets (deterministic, not ML); extract anchor
   hints from quoted strings, file-like tokens, `#id`/`.class` tokens,
   route-like tokens, and camelCase/PascalCase identifiers.
2. **Anchor detection** (`anchors.ts`) — score every initial BM25-hit
   candidate via A(u); the top scorers seed Stage B. A relative-only cutoff
   breaks when nothing has a strong match (many files tie at the bare
   floor value) — an absolute minimum (`ANCHOR_ABSOLUTE_MIN`) rejects that
   whole tied batch instead of anchoring on noise, falling back to the
   strongest raw text match if nothing clears it.
3. **Stage B expansion** (`anchors.ts::bfsDepths`) — BFS both import
   directions from the anchor set, capped at `maxExpansionDepth()`
   (2, or 3 for low-confidence/cross-cutting tasks per the spec). This
   same BFS distance feeds both Proximity's GraphDistance and the
   expansion multiplier E(depth) = exp(-0.7·depth) — the spec's "depth"
   and "graph distance" are the same traversal.
4. **Scoring** (`signals.ts`, `gitSignals.ts`) — the 9 components below.
5. **Confidence finalization** — two-pass: score once with C=0 to get the
   rank margin, compute the global confidence scalar, re-score every
   candidate with the real C baked in.
6. **Budget packing** (`budget.ts`) — tiers fall directly out of depth
   (0→primary, 1→supporting, 2+→optional — this *is* the spec's own
   Candidate Expansion depth semantics, including "schemas" being a
   depth-2/optional example). Within a tier, candidates are ordered by
   raw score first and utility density `U(u)=S'(u|q)/tokens` only as a
   tie-breaker between near-equal scores — pure density-first ordering
   lets a trivially cheap file (a 40-token constants file) crowd out a
   larger, genuinely more relevant one purely for being cheaper. That
   tradeoff makes sense for fine-grained symbol/property units (the
   spec's intent); at file granularity, token cost varies too widely to
   be a reliable proxy for relevance.

## Signal-by-signal mapping

| Signal | Spec formula | Glint's proxy |
|---|---|---|
| **H** Hierarchy | Base prior by level + task-type modifier, clamped | Exact — `LEVEL_BASE` + `H_MODIFIERS` tables |
| **M** Semantic Match | `0.45·BM25 + 0.35·EmbedSim + 0.20·ExactMatchBoost` | No embeddings (no guaranteed API key; `glint plan` must stay free/offline). EmbedSim's weight is redistributed proportionally: `0.692·BM25 + 0.308·ExactMatchBoost` |
| **D** Dependency | Direct/reverse edge, centrality, shared flow | Exact — computed from the existing import graph |
| **O** Ownership | Origin match, upstream control, config authority, mutation likelihood | Heuristic proxies: does the unit *define* the matched anchor; import/importedBy ratio; config/schema path patterns; presence of non-type-only bodies |
| **P** Proximity | `0.65·(1/(1+dist)) + 0.35·DirectorySimilarity` | Exact — same BFS distance as expansion, plus shared-path-prefix ratio |
| **R** Recency/Co-edit | `0.50·CoEdit + 0.30·RecentChange + 0.20·PRCoupling` | PRCoupling needs a PR-system API a local CLI doesn't have — stays 0, not redistributed (a minor sub-signal of an already-small top-level weight, `w_R=0.06`). RecentChangeBoost uses filesystem mtime (always available). CoEditFrequency uses a bounded `git log -n 200` and degrades to 0 outside a git repo |
| **V** Verification | Safety-critical, coverage link, regression risk | Heuristic: test/schema/contract path patterns; reverse-lookup whether any test file imports the candidate; centrality scaled by task type |
| **C** Confidence | `0.50·ClassifierConfidence + 0.30·AnchorAgreement + 0.20·RankMargin` | Classifier confidence from Stage A's keyword-bucket heuristic (not ML); anchor agreement = top anchor's own A(u); rank margin from the two-pass scoring |
| **N** Noise Penalty | Generated, duplication, low-utility, token waste | Exact patterns + reused file hashes (already computed by the indexer) for duplicate detection |

## Mode presets

Task types "emphasize" a set of components per the spec's examples
(`copy` → M, O; `style`/`ui` → H, D, O, P; `logic` → D, O; `data`/`api` →
D, O, V). Glint applies a flat ×1.25 multiplier to the listed weights —
the spec doesn't fix an exact magnitude, so this is a documented,
tunable constant (`EMPHASIS` in `weights.ts`).
