# handover

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)

A Claude Code skill that turns a session into a single self-contained prompt
— for a fresh context window, a teammate, or a task that hasn't been started
yet.

## The problem

Context windows close. Sessions get compacted, laptops get closed, work gets
picked up by someone else tomorrow. What usually survives that gap is
whatever you remember to type into the next session — which means the goal,
the decisions you already made and why, the dead end you already ruled out,
all ride on your memory instead of being written down properly. The next
session either re-asks you questions you already answered, or silently
guesses and gets it wrong.

The other direction fails just as often. A one-line idea — "build some kind
of dashboard for X" — gets handed to a fresh session with none of the
narrowing a real ticket would have: no scope, no constraints, no definition
of done. The session either stalls asking basic questions or starts building
the wrong thing.

`handover` is one skill that does both jobs, because they're the same
artifact viewed from opposite ends: a prompt with no gaps in it. When work
already happened, it distills the conversation into that prompt. When
nothing has happened yet beyond a rough idea, it interviews you until it can
write that same prompt from scratch.

## Install

Project-scoped:

```bash
npx skills add LorcanChinnock/handover
```

Global, so it's available in every project:

```bash
npx skills add LorcanChinnock/handover -g
```

## Usage

```
/handover
```

Or just say something like "hand this off", "write a handover prompt", or
"give me something to paste into a fresh chat" — it's wired to fire on those
too.

Pass an optional focus to narrow it to one thread instead of the whole
conversation:

```
/handover just the auth refactor part
```

## What it actually does

It runs one of two modes, decided from what's actually in the conversation
rather than how the request was phrased:

- **Distill** — work already happened (or is partway done). The job is
  compressing it faithfully: pull objective state by re-running `git status`,
  `git log`, and `git diff --stat` per repo involved rather than trusting
  memory, then pull what only the conversation knows — the goal, the why,
  decisions already made and their reasons, what's done, what's left,
  gotchas, and open questions.
- **Expand** — little or nothing has been built yet. The job is drawing the
  brief out of you through dialogue: one question at a time, each with a
  recommendation attached so you can confirm rather than invent an answer,
  stopping once further questions would be diminishing returns rather than
  interrogating every possible parameter.

Both modes end at the same fixed template — Objective, Background, Current
state, Done, Remaining, Decisions already made, Gotchas, How to verify
success, Relevant files, Open questions — printed as a single fenced block so
it copies in one action, omitting any section that's empty rather than
padding it with "None."

Before printing, it self-checks against the thing that actually matters:
could a reader with zero shared history start acting on this without asking
you anything you already know the answer to? Anything derivable from a
five-second read of the code gets cut, since the point is saving research,
not writing a tour.

`handover` only ever prints the prompt. It doesn't commit, push, open a PR,
or file a ticket on your behalf, even in Expand mode where the brainstormed
idea would eventually need one.

## Contributing

Bug reports and small, focused proposals are welcome — see
[CONTRIBUTING.md](./CONTRIBUTING.md).

## License

[MIT](./LICENSE)

## Links

- [Claude Code Agent Skills docs](https://docs.claude.com/en/docs/claude-code/skills)
- [`npx skills` CLI](https://www.npmjs.com/package/skills)

Built by [Lorcan Chinnock](https://github.com/LorcanChinnock).
