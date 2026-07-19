# Glint — landing page brief

## One line

**Any AI coding agent. Only the changes you meant.**

## What Glint is

A local-first command-line tool that sits between you and whichever AI coding
agent you use — Claude Code, Cursor, or ChatGPT Codex. It is not another agent.
It is the layer that makes the agent you already pay for do what you actually
asked, and makes your setup work identically across all of them.

Install: `npm install -g getglint` → command is `glint`.

## The problem it solves

Two problems, both familiar to anyone who codes with agents daily.

**1. Agents do more than you asked.** You say "remove the button in the navbar"
and it removes both identical buttons, or rewrites the text instead of deleting
it, or tidies three files you never mentioned. The intelligence is real; the
obedience isn't.

**2. Your setup is locked to one vendor.** Rules live in `CLAUDE.md`, or
`.cursorrules`, or `AGENTS.md` — each tied to one tool. Switch agents and you
start over. Nobody will fix this, because no vendor has a reason to make their
rules work in a competitor's product.

## What it actually does

**Understands the request before spending anything.** Glint reads your codebase
structure and classifies the task into four bands: execute it, infer the
obvious, ask a question, or refuse. If two identical buttons match your
description, it asks which one instead of guessing. If the target doesn't exist,
it says so before an agent burns a single token looking.

**One set of rules, every agent.** Glint reads `CLAUDE.md`, `.cursorrules`,
`AGENTS.md`, `.glint/rules.md`, `context.md`, and skill files — then applies all
of them to whichever agent runs. Write a rule once; it holds in Claude Code,
Cursor, and Codex alike.

**Verifies afterwards, not just instructs.** A rule saying "never touch
`src/generated`" is checked after the run, whichever agent ran, with an offer to
restore anything that broke it. Instructions are hope; verification is control.

**Runs multi-agent pipelines.** `glint flow "audit auth with claude, then fix it
with cursor, then review the diff with codex"` — one command, each step routed
to the agent you named, output passed forward. This is the thing no vendor can
ship.

**Compares agents on the same task.** `glint compare "..."` runs one task
through every connected agent and lets you keep the best result.

**Shows what changed in meaning.** Not just "+12 −4" but "styles and attributes
only — no symbols or copy affected." Plus `glint revert` to undo.

## Who it's for

Developers who use AI coding agents every day and have been burned by one
changing more than they asked. Especially people who use more than one agent, or
who work on a codebase where an unintended edit is expensive.

## Tone

Calm, precise, technical. This is a tool for people who have already been
disappointed by AI tooling marketing. Understatement lands better than hype.
Short sentences. No exclamation marks. No "revolutionary", no "10x", no
"supercharge". The audience will judge the copy as evidence of the product's
taste.

Visual direction: terminal-first. The product IS the CLI, so show real terminal
output rather than invented dashboard UI. Existing brand colors: `#0040FF`
(blue) on `#F3F9FF` (near-white).

## Suggested page structure

1. Hero: the one-liner, an install command, one honest terminal recording
2. The problem: the "it removed both buttons" story — concrete, not abstract
3. Rules that follow you across agents (the portability argument)
4. `glint flow` — the multi-agent pipeline, with real output
5. Verification: what Glint checks after the agent finishes
6. Install + link to GitHub

## What NOT to claim

The landing page must not outrun the product. Specifically:

- **Do not claim token or cost savings.** Glint no longer displays token counts
  or cost, and those numbers were never reliable on a subscription. Say Glint
  sends only the relevant part of the repo — that is true and verifiable — but
  do not put a number or a percentage on money saved.
- **Do not claim every flow step is safety-checked.** `glint flow` currently
  bypasses the band classification and clarification that `glint run` applies.
  Describe flow as orchestration, not as guarded.
- **Do not imply Glint is an AI or has a model.** It has no model of its own and
  brings no intelligence. It orchestrates the agent you already pay for. Saying
  otherwise invites the first reviewer to call it a wrapper.
- **Do not list agents that aren't supported.** Today: Claude Code, Cursor,
  ChatGPT Codex. Not Copilot, not Windsurf, not Gemini.
- **No fabricated social proof.** No invented testimonials, logos, user counts,
  or star counts. The product is days old; inventing traction is both dishonest
  and trivially checkable.

## Honest current state

Glint is early. It works, it is published, and it is genuinely useful — but it
has been public for roughly a week and has no meaningful user base yet. Position
it as a sharp new tool rather than an established one. That framing is both
truthful and more appealing to the early-adopter audience it needs.
