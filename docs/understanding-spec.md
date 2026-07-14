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

## Deterministic proxies (where we diverge from the spec)

- **No ML intent parser.** The intent frame is keyword/regex. It's tuned for
  the MVP surface (React/Next/HTML/CSS UI edits) and errs toward classifying
  destructive verbs correctly, since that's what gates Red.
- **Shared-component detection ≈ duplicate-text + `crossFile`.** The spec wants
  a detector that knows one component definition renders many instances. Glint
  approximates "broad impact" with the duplicate copy spanning multiple files
  (`crossFile`) or multiple landmarks (`crossSection`). A true definition→
  instances graph would need symbol-level resolution (a language server).
- **Cross-breakpoint detector: not implemented.** Responsive desktop/mobile
  variant analysis is out of MVP scope.
- **Section-collision** is covered by the duplicate detector's `crossSection`
  flag and the file-scope question, not a separate route-level model.
- **Evaluation simulator: not built.** The spec itself lists the interactive
  simulator as the "next build step"; it's deferred until the band thresholds
  need tuning against a labelled task library.

## Where behavior lives

- `src/core/understanding.ts` — pure/offline: intent frame, detectors,
  resolution confidence, band classifier. No prompts.
- `src/core/clarify.ts` — composes interactive questions from the report
  (`assessTask`, `buildQuestions`, `runQuestions`) and compiles answers back
  into the task.
- `src/commands/run.ts` — enforces the band: prints it, blocks Red in
  non-interactive mode, asks Orange/Red, injects the Yellow style note.
