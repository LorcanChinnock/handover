# Contributing

Thanks for considering it. `handover` does one job — produce a prompt a
stranger with an empty context window could act on — so most proposed
changes should be judged against whether they make that prompt more
self-contained, not whether they add a nice-to-have feature.

## Reporting bugs

Open an [issue](https://github.com/LorcanChinnock/handover/issues) with the
prompt it produced and what was missing or wrong in it. "It skipped the
gotcha about the flaky test" is far more actionable than "the output wasn't
great," because it points at which section of the template or which part of
Step 1 needs a look.

## Proposing changes

For anything past a typo fix, open an issue before a PR — five minutes of
discussion up front is cheaper than a PR that gets rejected on a values
disagreement rather than a code one.

The value that matters here: **the output has to stand alone.** A change
that adds a section, a mode, or a heuristic is worth it only if it makes the
printed prompt more likely to be actionable with zero shared history. A
change that makes the skill assume more about what the reader already knows,
or blur the line between Distill and Expand, is going to get pushed back by
default.

A few things that keep resurfacing and aren't actually improvements:

- **Merging Distill and Expand into one flow.** They solve opposite
  problems — one has the material and needs to compress it, the other has
  none and needs to draw it out through dialogue. Collapsing them produces a
  mode that's mediocre at both.
- **Having the skill take action beyond printing the prompt** (committing,
  opening a PR, filing a ticket). That's a deliberate boundary, not a gap —
  see "What this produces" in `SKILL.md`.
- **Adding sections to the template "just in case."** Every section it
  writes is one the reader has to read past even when it's empty. Prefer
  cutting a section over adding one.

## Pull requests

- Keep the diff scoped to what the linked issue describes.
- If behavior changes, update `SKILL.md` and `README.md` together — they're
  not allowed to describe two different skills.
- Conventional commit messages (`feat:`, `fix:`, `docs:`, ...) drive
  `release-please`'s changelog and versioning — see `CLAUDE.md` for how
  releases work in this repo.
