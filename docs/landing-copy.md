# Super Terminal — landing page copy (SEO + AEO)

Everything below is ready to hand to a build agent. Sections marked **[VERIFY]**
contain facts that must be confirmed before the page goes live.

---

## 0. Naming and entity strategy — read first

"Super Terminal" competes for search attention with terminal emulators (iTerm2,
Windows Terminal, Warp, Ghostty, Hyper). Someone searching the bare phrase most
likely wants a terminal app, not an AI control layer. Three rules follow:

1. **Never use the bare name in a title or heading without a qualifier.** Always
   "Super Terminal — the control layer for AI coding agents", never just
   "Super Terminal".
2. **Do not chase the head term.** You will lose "terminal" to established
   products. Win the long tail instead: intent-rich phrases where you are the
   only sensible answer (see §2).
3. **Build the entity deliberately.** Same name, same one-line description, same
   logo, everywhere: site, GitHub, npm, Product Hunt, X, LinkedIn, Crunchbase.
   Answer engines resolve entities by corroboration across sources. Inconsistent
   descriptions read as different products and dilute all of them.

---

## 1. Page metadata

```html
<title>Super Terminal — One Control Layer for Claude Code, Cursor & Codex</title>

<meta name="description" content="Super Terminal is a command-line control layer for AI coding agents. Write your rules once and they apply to Claude Code, Cursor, and ChatGPT Codex alike — and chain agents into one workflow. Free and local-first.">

<link rel="canonical" href="https://superterminal.dev/">

<meta property="og:type" content="website">
<meta property="og:title" content="Super Terminal — One Control Layer for Claude Code, Cursor & Codex">
<meta property="og:description" content="Your rules, your context, your skills — applied to every AI coding agent. Chain agents into one workflow. Free and local-first.">
<meta property="og:image" content="https://superterminal.dev/og.png">
<meta property="og:url" content="https://superterminal.dev/">

<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Super Terminal — One Control Layer for AI Coding Agents">
<meta name="twitter:description" content="Write your rules once. Every agent follows them. Chain Claude Code, Cursor, and Codex into one workflow.">
<meta name="twitter:image" content="https://superterminal.dev/og.png">
```

Title is 63 characters, description 231 — both within display limits. The
description leads with a definition because answer engines quote it directly.

---

## 2. Keyword strategy

**Primary (page-level intent)**
- control layer for AI coding agents
- use Claude Code and Cursor together
- AI coding agent rules across tools
- multi-agent coding workflow

**Secondary (section-level)**
- CLAUDE.md and .cursorrules in one place
- AI agent changed files I didn't ask for
- run Claude Code and Codex in one command
- compare AI coding agents on the same task
- AI coding agent guardrails

**Long-tail question phrases (AEO — one per FAQ entry)**
- how do I use the same rules in Claude Code and Cursor
- how do I stop an AI agent from changing too much code
- can I run more than one AI coding agent on the same task
- what is the difference between Super Terminal and Claude Code
- do I need a separate subscription for Super Terminal
- is there a CLI that works with any AI coding agent

**Do not target**: "terminal", "best terminal", "terminal emulator". Wrong
intent, unwinnable, and attracts traffic that bounces — which teaches search
engines your page doesn't satisfy the query.

---

## 3. Hero

**H1**
> One control layer for every AI coding agent

**Subhead**
> Super Terminal is a free, local-first CLI that sits between you and Claude
> Code, Cursor, or ChatGPT Codex. Write your rules once — every agent follows
> them. Chain agents into a single workflow. Keep only the changes you meant.

**Primary CTA:** `Install — it's free` → install section
**Secondary CTA:** `View on GitHub` → repo

**[VERIFY]** Install command. The published npm package is currently
`getglint`, exposing the `glint` command. Do **not** ship an install snippet
using the new name until the renamed package is actually published — a landing
page whose main call to action fails on paste is worse than no landing page.
Either publish the rename first, or label the block "coming as `superterminal`;
today: `npm install -g getglint`".

**Hero visual:** a real terminal recording, not a mockup dashboard. Show one
`flow` command routing steps across two agents. Real output is the proof; an
invented UI signals the product doesn't exist yet.

---

## 4. The problem (lead with the story, not the abstraction)

**H2** — Your agent is brilliant. It is not obedient.

You ask it to remove one button in the navbar. There are two identical buttons
on the page, so it removes both. You ask it to change some copy, and it also
reformats three files you never mentioned. The intelligence is real. The
obedience is not.

Then there's the second problem, which is quieter and more expensive.

Your rules live in `CLAUDE.md`. Or `.cursorrules`. Or `AGENTS.md`. Each file
belongs to one tool. Switch agents — or work on a team where people use
different ones — and everything you taught the first agent is gone. No vendor
will fix this. Anthropic has no reason to make your rules work inside Cursor,
and Cursor has no reason to make theirs work in Codex.

That's the gap Super Terminal fills. It is deliberately not an agent.

---

## 5. What it does

Four H3 blocks under one H2 — **H2: What Super Terminal does**

### It asks before it guesses

Super Terminal reads your codebase structure and sorts every request into one of
four bands: run it, infer the obvious detail, ask a clarifying question, or
refuse outright. When your description matches two identical elements, it asks
which one — instead of changing both and letting you find out in code review.
When the thing you named doesn't exist, it tells you before an agent starts
searching for it.

### Your rules follow you to every agent

Super Terminal reads the instruction files you already have — `CLAUDE.md`,
`.cursorrules`, `AGENTS.md`, `.glint/rules.md`, `context.md`, and skill files —
and applies all of them to whichever agent runs the task. Write a standard once
and it holds in Claude Code, in Cursor, and in Codex. Drop a `context.md` in
your project and every agent gets the same background.

### It verifies after the run, not just before

A rule saying "never modify `src/generated`" is not a suggestion here. Super
Terminal checks the files that actually changed once the agent is finished —
whichever agent it was — and offers to restore anything that broke the rule.
Telling an agent what not to do is hope. Checking afterwards is control.

### It chains agents into one workflow

```bash
superterminal flow "audit auth with claude,
                    then fix the findings with cursor,
                    then review the diff with codex"
```

One command. Each step runs on the agent you named, and each step's output is
handed to the next. This is the capability no single vendor can ship, because
shipping it means routing your work to a competitor.

---

## 6. Comparison table (high AEO value — answer engines lift these directly)

**H2** — Super Terminal vs. using an agent directly

| | Agent on its own | With Super Terminal |
|---|---|---|
| Rules | One file per tool, rewritten per agent | Written once, applied to every agent |
| Ambiguous request | Agent guesses | You're asked which one you meant |
| Rule enforcement | Requested in a prompt, unverified | Checked against files actually changed |
| Multiple agents | Separate sessions, manual copy-paste | One command, output passed forward |
| Comparing agents | Run each by hand and diff | `compare` runs all of them on one task |
| Undo | `git checkout` and hope | Semantic diff, then targeted revert |
| Cost | Your existing subscription | Same subscription — Super Terminal is free |

---

## 7. Who it's for

**H2** — Who this is for

- **Developers using agents daily**, who have been burned by one changing more
  than they asked
- **Teams standardising on AI**, who need one set of standards that holds no
  matter which agent an individual prefers
- **Anyone running more than one agent** — the workflow nobody else supports
- **People working in expensive codebases**, where an unintended edit costs real
  money or real trust

Not for you if you only ever use one agent, never write project rules, and are
happy reviewing every diff by hand.

---

## 8. FAQ — write these as literal Q&A (this is the AEO engine)

Each answer opens with a complete, self-contained sentence that makes sense
quoted alone, with no pronouns pointing back at the question. That single habit
is most of what makes content quotable by an answer engine.

**H2** — Frequently asked questions

**What is Super Terminal?**
Super Terminal is a free, local-first command-line tool that acts as a control
layer between a developer and their AI coding agent. It works with Claude Code,
Cursor, and ChatGPT Codex, applying one set of project rules to all of them and
allowing several agents to be chained into a single workflow.

**Is Super Terminal an AI or a coding agent?**
No. Super Terminal contains no AI model of its own and does not generate code.
It orchestrates the AI coding agent you already use, adding clarification before
a task runs and verification after it finishes.

**Do I need another subscription?**
No. Super Terminal is free and uses the agent subscription you already pay for —
Claude Code, Cursor, or ChatGPT. It never asks for a separate API key of its
own.

**How do I use the same rules in Claude Code and Cursor?**
Keep your rules in any file those tools already read — `CLAUDE.md`,
`.cursorrules`, `AGENTS.md`, or a shared `context.md`. Super Terminal reads all
of them and passes their contents to whichever agent runs your task, so one
written standard applies to every agent without duplication.

**How do I stop an AI agent from changing code I didn't ask about?**
Super Terminal reduces unintended edits two ways. Before the run, it classifies
ambiguous requests and asks which target you meant rather than guessing. After
the run, it checks the files that actually changed against your project's
protected paths and offers to restore anything that shouldn't have been touched.

**Can I run more than one AI coding agent on the same task?**
Yes. The `compare` command runs one task through every connected agent and lets
you keep the result you prefer. The `flow` command does the opposite: it splits
a job into steps and routes each step to a different agent, passing output
forward.

**What is the difference between Super Terminal and Claude Code?**
Claude Code is an AI coding agent that writes code. Super Terminal is not an
agent and writes no code — it is a layer that runs on top of Claude Code, Cursor,
or Codex. The two are complementary: Super Terminal makes the agent follow your
project's rules and lets you use several agents together.

**Which agents are supported?**
Claude Code, Cursor, and ChatGPT Codex. **[VERIFY before publishing — update
this list if support changes; an inaccurate list here is the fastest way to lose
a technical reader's trust.]**

**Does Super Terminal send my code anywhere?**
Super Terminal runs locally and sends your code only to the AI agent you have
chosen — the same place it already goes when you use that agent directly. It
adds no separate destination for your source code. **[VERIFY — the product does
send anonymous usage counts, containing no code, filenames, or prompts. If that
is enabled in the shipped build, say so plainly here and link a privacy page.
Being caught omitting it costs more than disclosing it.]**

**Is it open source?** **[VERIFY — answer only if the licence is decided.]**

---

## 9. Install section

**H2** — Install

```bash
npm install -g superterminal    # [VERIFY — package not yet published under this name]
superterminal connect           # one-time: pick your agent
superterminal run "add a loading state to the checkout button"
```

Requires Node.js 18+. Works on macOS, Linux, and Windows. **[VERIFY the Windows
claim by actually running it on Windows — an unverified OS claim generates
support issues and bad first impressions.]**

---

## 10. Closing CTA

**H2** — Keep the agent. Lose the surprises.

Super Terminal is free, installs in one command, and works with the AI agent you
already use.

`npm install -g superterminal` · View on GitHub

---

## 11. Structured data (JSON-LD) — paste in `<head>`

Answer engines and rich results both read this. The `FAQPage` block should
mirror §8 exactly; mismatched copy risks the markup being ignored.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@graph": [
    {
      "@type": "SoftwareApplication",
      "name": "Super Terminal",
      "applicationCategory": "DeveloperApplication",
      "applicationSubCategory": "Command Line Tool",
      "operatingSystem": "macOS, Linux, Windows",
      "description": "A free, local-first command-line control layer for AI coding agents. Applies one set of project rules to Claude Code, Cursor, and ChatGPT Codex, and chains multiple agents into a single workflow.",
      "url": "https://superterminal.dev/",
      "offers": {
        "@type": "Offer",
        "price": "0",
        "priceCurrency": "USD"
      },
      "softwareRequirements": "Node.js 18 or later"
    },
    {
      "@type": "FAQPage",
      "mainEntity": [
        {
          "@type": "Question",
          "name": "What is Super Terminal?",
          "acceptedAnswer": {
            "@type": "Answer",
            "text": "Super Terminal is a free, local-first command-line tool that acts as a control layer between a developer and their AI coding agent. It works with Claude Code, Cursor, and ChatGPT Codex, applying one set of project rules to all of them and allowing several agents to be chained into a single workflow."
          }
        },
        {
          "@type": "Question",
          "name": "Is Super Terminal an AI or a coding agent?",
          "acceptedAnswer": {
            "@type": "Answer",
            "text": "No. Super Terminal contains no AI model of its own and does not generate code. It orchestrates the AI coding agent you already use, adding clarification before a task runs and verification after it finishes."
          }
        },
        {
          "@type": "Question",
          "name": "Do I need another subscription to use Super Terminal?",
          "acceptedAnswer": {
            "@type": "Answer",
            "text": "No. Super Terminal is free and uses the agent subscription you already pay for, such as Claude Code, Cursor, or ChatGPT. It does not require a separate API key of its own."
          }
        },
        {
          "@type": "Question",
          "name": "How do I use the same rules in Claude Code and Cursor?",
          "acceptedAnswer": {
            "@type": "Answer",
            "text": "Keep your rules in any file those tools already read, such as CLAUDE.md, .cursorrules, AGENTS.md, or a shared context.md. Super Terminal reads all of them and passes their contents to whichever agent runs the task, so one written standard applies to every agent without duplication."
          }
        },
        {
          "@type": "Question",
          "name": "Can I run more than one AI coding agent on the same task?",
          "acceptedAnswer": {
            "@type": "Answer",
            "text": "Yes. The compare command runs one task through every connected agent so you can keep the result you prefer. The flow command splits a job into steps and routes each step to a different agent, passing each step's output forward to the next."
          }
        },
        {
          "@type": "Question",
          "name": "What is the difference between Super Terminal and Claude Code?",
          "acceptedAnswer": {
            "@type": "Answer",
            "text": "Claude Code is an AI coding agent that writes code. Super Terminal is not an agent and writes no code; it is a layer that runs on top of Claude Code, Cursor, or Codex. They are complementary, since Super Terminal makes the agent follow project rules and lets several agents be used together."
          }
        }
      ]
    }
  ]
}
</script>
```

---

## 12. Technical SEO checklist

- **One `<h1>` only.** Section headings are `<h2>`; sub-points `<h3>`.
- **Real HTML text, not images.** Terminal output in a `<pre>` block is
  crawlable; a screenshot of the same output is not. Use real text with syntax
  colour, and reserve images for genuine visuals.
- **Descriptive `alt` on every image.** "Super Terminal routing a three-step flow
  across Claude Code and Cursor", not "screenshot".
- **Fast and static.** No client-side rendering for the copy. Answer engines are
  far less reliable at executing JavaScript than browsers are.
- **Semantic HTML.** `<main>`, `<section>`, `<nav>`, `<footer>`. The FAQ as a
  definition list or headed sections — not an accordion that hides text from
  crawlers unless it's rendered in the DOM regardless of open state.
- **`robots.txt` must allow AI crawlers** if you want AEO traffic — GPTBot,
  ClaudeBot, PerplexityBot, Google-Extended. Blocking them is a valid choice, but
  it is incompatible with wanting to be cited by answer engines. Decide
  deliberately.
- **`sitemap.xml`**, submitted in Google Search Console and Bing Webmaster Tools.
  Bing matters more than its share suggests — it feeds several answer engines.
- **Open Graph image at 1200×630**, with the product name legible at thumbnail
  size.

---

## 13. AEO principles applied here (so future pages stay consistent)

1. **Answer first, elaborate second.** Every section opens with the conclusion.
   Answer engines extract opening sentences.
2. **Self-contained sentences.** No "it", "this", or "the above" pointing at a
   previous paragraph. Each quotable sentence must survive alone.
3. **Define the entity explicitly and repeatedly.** "Super Terminal is a…"
   appears verbatim several times. Repetition across sources is how an answer
   engine becomes confident about what a thing is.
4. **Comparison tables.** These get quoted more than prose, because they are
   already structured.
5. **Name competitors accurately.** Being the page that explains the difference
   between Super Terminal and Claude Code means you get surfaced for that
   question. Describe them fairly — an unfair comparison gets discounted.
6. **Consistency across the web.** Ship the same one-line description on GitHub,
   npm, Product Hunt, and X. Corroboration is what resolves an entity.

---

## 14. Accuracy rules — do not violate these

The audience is developers who will test every claim within minutes.

- **No token or cost savings claims.** The product no longer displays token
  counts or costs, and those figures were never reliable on a subscription plan.
  You may say it sends only the relevant part of the repository. Do not attach a
  number or a percentage to money saved.
- **Do not describe `flow` as safety-checked.** The multi-step flow currently
  bypasses the clarification and classification that single runs apply. Present
  it as orchestration.
- **No invented social proof.** No testimonials, no company logos, no user
  counts, no star counts, no "trusted by" strip. The product is days old, and
  every one of those claims is trivially checkable.
- **No fabricated benchmarks.** Do not publish accuracy or speed percentages
  without a reproducible methodology on the page.
- **Keep the supported-agent list current.** It is the single most likely fact to
  go stale and the most damaging when wrong.
