# Glint Context Understanding & Clarification — implementation notes

Glint's understanding layer (`src/core/understanding.ts`, driven from
`src/core/clarify.ts` and `src/commands/run.ts`) implements the Context
Understanding & Clarification Specification. It sits **on top of** the
ranking system (`src/core/ranking/`), which is untouched: the ranking
answers *what is relevant*; this layer answers *what is safely
resolvable*, then chooses one of four outcomes. As with the ranking
notes, this records where a deterministic, local-first proxy stands in
for something the spec names but a CLI with no ML/embeddings can't
compute directly.

## Pipeline mapping

| Spec module | Implementation |
| --- | --- |
| Intent Frame Builder | `buildIntentFrame()` — keyword/regex classifier producing `{ action, risk, modifiers, scopeHints, quotedTargets }`. No ML; the action list is ordered so destructive verbs win. |
| Candidate Instance Graph | `detectDuplicate()` scans the **selected** files (primary + supporting) for real elements. Each `Instance` carries file, line, enclosing landmark, visible text. We don't build a separate instance graph — the ranking already produced the candidate set; we enrich it with location/landmark metadata on demand. |
| Relevance Scorer | The existing ranking (`rankContext`) — reused as-is. |
| Resolution Confidence Model | `resolutionConfidence()` / `rankingIsConfident()` — derived from the **anchor margin** (top-1 vs top-2), i.e. the spec's ambiguity margin made relative to the top score rather than a fixed threshold. |
| Ambiguity Detector Suite | `detectAmbiguity()` → duplicate-text (`detectDuplicate`) + `styleUnderspecified`. See proxies below. |
| Style Inference Engine | `styleUnderspecified` + the Yellow band inject a **style-continuation note** into the task (reuse existing colors/spacing/variants) rather than computing a design-token graph. |
| Clarification Composer | `duplicateQuestion()` in `clarify.ts` — evidence-backed: names the copy and where each instance lives ("the navbar and the footer"), with an "All of them" option. |

## Four bands

`classifyBand()` returns one of:

- **Green** — a dominant anchor and no active collision → auto-execute.
- **Yellow** — target resolved but styling underspecified (`restyle`/vague
  aesthetic request with no concrete color/size) → continue the existing
  design and execute.
- **Orange** — a target genuinely resolves to several candidates (duplicate
  copy, or ≥3 candidate files the ranking can't separate) → ask one focused
  question.
- **Red** — a **destructive** action (`remove`/`hide`/`move`) collides with the
  same copy in different sections/files → block. Interactively this forces the
  clarification; with `--yes`/no TTY the run **stops** with the evidence rather
  than editing every occurrence.

The band is printed on every run (`printBand`) so the safety decision is
interpretable, per the spec's color-coded-bands requirement.

## Semantic element graph (the foundation)

Detection no longer reads code as text. `src/core/semantic/` parses real
structure into an element graph:

- **HTML → parse5** (`html.ts`): DOM walk producing elements with ancestry,
  enclosing landmark, static text, attributes, and source line numbers.
- **JSX/TSX → ts-morph** (`jsx.ts`): JSX elements and self-closing tags with
  tag/component role, static attribute values, static vs. dynamic (`{…}`) text,
  ancestry/landmark, and two blast-radius facts — `inLoop` (rendered inside
  `.map`/`.forEach`/`.flatMap`) and the component name (so instances resolve to
  a shared definition).
- `graph.ts` merges both and indexes component definitions and their
  instantiation sites.

Because this is AST/DOM-derived, multi-line elements, React components, and
list rendering are represented faithfully — the class of bug that regex kept
reintroducing is gone by construction.

## Deterministic proxies (where we still diverge from the spec)

- **No ML intent parser.** The intent frame is keyword/regex, tuned for the
  React/Next/HTML/CSS surface and biased toward classifying destructive verbs
  correctly (that's what gates Red).
- **Shared-component detection is real now.** `sharedComponent` is true when the
  duplicate instances are the same component role from the graph; `inLoop` marks
  list-rendered targets. Both feed the broad-impact → Red decision. (Full
  cross-module definition resolution via a type-checker is still future work;
  today it keys on component name within the scanned files.)
- **Cross-breakpoint detector: not implemented.** Responsive desktop/mobile
  variant analysis is out of scope.
- **Calibrated confidence: partial.** Bands are rule-based over graph facts; a
  learned/calibrated confidence model is the next step, and the eval harness is
  the instrument that would calibrate it.

## Measuring accuracy

`test/eval.test.ts` is the evaluation harness: a labeled case library (seeded
with every real failure) scored for band accuracy and the spec's over-ask /
under-ask / **destructive-miss** rates. `destructive-miss` is asserted `== 0` —
the precision-via-abstention guarantee (never silently make a broad destructive
edit). Grow accuracy by adding cases here first, then fixing the engine.

## Where behavior lives

- `src/core/semantic/` — AST/DOM element graph. Pure/offline.
- `src/core/understanding.ts` — intent frame, graph-backed detectors, band
  classifier. No prompts.
- `src/core/clarify.ts` — composes interactive questions from the report
  (`assessTask`, `buildQuestions`, `runQuestions`) and compiles answers back
  into the task.
- `src/commands/run.ts` — enforces the band: prints it, blocks Red in
  non-interactive mode, asks Orange/Red, injects the Yellow style note.
- `test/eval.test.ts` — the accuracy harness and regression gate.
