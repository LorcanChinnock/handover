---
name: handover
description: >
  Use when the user runs /handover, or asks to "hand this off", "write a
  handover prompt", "generate a follow-up prompt for a new session", "give me
  something to paste into a fresh chat", or wants to end the current task and
  continue it later with a clean context window. Also use when the user
  opens a fresh session with a rough, underspecified idea and wants help
  turning it into a complete brief — e.g. "/handover I want to build some
  kind of dashboard for X" or "help me flesh this prompt out before I run
  it" — treat this as the same skill in reverse: instead of distilling
  history into a prompt, it brainstorms with the user to expand a vague ask
  into one. Either way it produces a single self-contained prompt, ready to
  paste into (or already sitting in) a session with no other context,
  front-loading the goal, state, decisions, gotchas, and success criteria so
  little to no extra research or back-and-forth is needed before acting.
  Accepts an optional focus argument to narrow a handover to one thread
  instead of the whole session.
allowed-tools:
  - Bash
  - Read
  - Write
  - AskUserQuestion
---

You do one of two jobs, both ending in the same artifact: a single self-contained prompt, printed as a copy-paste block, written for a reader with an empty context window — not this conversation continued, but a stranger who wasn't here for any of it. Everything that reader needs has to be *in the prompt*. If they'd have to ask you something you already knew, or redo research you already did, the handover failed at its one job.

- **Distill** — a task already happened (or is partway done) in this conversation, and it needs to survive the window closing. You already have the material; the job is compressing it faithfully.
- **Expand** — the ask is a rough, one-line idea and little or nothing has actually been built yet. You don't have the material; the job is drawing it out of the user through dialogue before writing anything down.

## Step 1 — which job is this

Look at what's actually in the conversation, not just how it was phrased. Real work, decisions, or file changes already present → Distill. A vague ask with no meaningful progress behind it yet → Expand. If it's genuinely ambiguous (e.g. some work happened but the user's request reads like they want to keep exploring, not wrap up), ask directly rather than guessing which mode to run.

## Distill

### Scope
If the user gave a focus (an argument, or "just the X part"), cover only that thread — goal, decisions, state relevant to it. A new session picking up "fix the flaky test" doesn't need the backstory of an unrelated piece of work from earlier in the conversation. With no focus, cover everything still open, plus enough of what's done that the remaining work makes sense.

If it's unclear what "the follow-up" even refers to — several unrelated threads were open and the user just said "/handover" — ask one quick question. Otherwise proceed.

### Pull objective state, don't recall it from memory
Anything checkable, check — don't rely on what you remember doing earlier, since files may have changed since. Per repo involved:

```bash
git -C <repo> rev-parse --abbrev-ref HEAD
git -C <repo> status --porcelain
git -C <repo> log --format='%h %s' -10
git -C <repo> diff --stat
```

If more than one repo is involved, run this for each — the new session might open in any of them and needs to know how they relate. If something like a PR is already open for this work, pull its identifying details too, so the new session links to it instead of rediscovering it.

### Pull what only this conversation knows
Git state shows *what* changed, not *why*, what was tried and rejected, or what's intentionally left undone. Scan the conversation for:

- **The goal** — concrete: not "improve the code" but "add a test for the null-tenant case in `getInvoice`, then remove the `TODO` at line 42."
- **Why** — the motivating request or problem. A reader who doesn't know why will make different tradeoffs than you did.
- **Decisions already made and their reasons** — anything chosen, corrected, or confirmed along the way. State these as settled, not as options to re-open. Omitting one means the new reader re-asks the question or silently picks the other answer.
- **What's done** — concrete, with `file:line` references where useful, not "made progress."
- **What's explicitly left** — ordered if order matters; distinguish "not started" from "started and blocked on X."
- **Gotchas** — dead ends, things that looked right but weren't, surprising constraints. The most expensive thing to omit — without them the new reader repeats a mistake you already paid for.
- **Open questions** — anything still undecided that the reader should either ask the user about or use judgment on.

Don't pad this with anything a fresh read of the code would show anyway. The point is saving research, not writing a tour.

## Expand

Treat the user's rough prompt as a first draft, not a finished ask — your job is to make it concrete enough that a stranger could execute it without coming back to ask the basics.

Brainstorm like a colleague scoping the work with them, not a form: one question at a time, your best recommendation attached to it so they can just confirm rather than invent an answer from nothing, and real follow-ups based on what they say rather than a fixed checklist. Keep going until you could fill in an objective, the reasons behind it, the constraints that matter, and what success looks like — all concretely — without inventing any of it yourself. Vague answers ("something like a dashboard") are worth one more round of narrowing before you write anything down; guessing at the missing specifics defeats the point of asking at all.

Stop asking once further questions would be diminishing returns — you don't need to interrogate every possible parameter, only the ones that would change what gets built or how.

## Write the prompt

Both modes end here. Use this template, omitting a section entirely when it's empty — Expand mode in particular may have nothing yet for "Done" or "Gotchas" — rather than writing "None," which is noise the reader has to read past.

```
You're picking up this work with a clean context window. Treat the background below as ground
truth for *why* things are the way they are, but verify state before acting where the prompt
tells you to check something yourself — time may have passed since this was written.

## Objective
<one to three sentences: exactly what to accomplish>

## Background
<why this exists — the motivating request, problem, or idea, and constraints that shaped it>

## Current state
- Repo: <path> — branch `<branch>` (based on `<base-branch>`)
- <clean / N uncommitted files — summary>
[repeat per repo if more than one is involved]

## Done
- <concrete, file:line where useful>

## Remaining
- <ordered list of what's left; mark blockers explicitly>

## Decisions already made (don't re-litigate)
- <decision> — why: <reason>

## Gotchas
- <what didn't work / surprised us> — <what to do instead>

## How to verify success
- <exact commands or checks, and what "done" looks like>

## Relevant files
- <path:line> — <why it matters>

## Open questions
- <anything to ask the user or decide with judgment>
```

Print a one-line header first (`### Handover prompt`), then the whole thing in one fenced block so it copies in a single action. Nothing above or below the fence except that header and, if you asked a scoping question earlier, a one-line note on what you scoped it to. In Expand mode, also note in one line whether this is meant to run right here in the current session or be carried elsewhere — whichever the user implied — since that changes nothing about the prompt itself but is worth confirming.

## Self-check before printing

- Could the reader start acting on this without asking you anything you already know the answer to? Any sentence assuming shared history ("as we discussed", "the thing you found earlier") needs to be spelled out instead.
- Does every "done" or "the state is X" claim trace back to something actually checked, not just remembered or assumed?
- In Expand mode: did every open question get resolved through the dialogue, or carried forward honestly into "Open questions" — not silently filled in with a guess?
- Is anything in here derivable from a five-second read of the code? Cut it.

## What this produces

A brief, not the work itself, and not a durable record — it's a one-time handoff or brainstorm output, not a substitute for notes meant to persist across many unrelated tasks. It doesn't take any action beyond printing the prompt: no committing, no opening pull requests, no filing tickets, even if the brainstormed idea would eventually need one.
