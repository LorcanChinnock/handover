# CLAUDE.md

Working notes for developing `handover` itself in this repo — not for
whatever project has `handover` installed as a skill.

## Testing changes to SKILL.md

Editing this repo's `SKILL.md` does not update an already-installed copy of
the skill. `npx skills add LorcanChinnock/handover -g` copies the file into
`~/.claude/skills/handover/` at install time; later edits to the repo's copy
have no effect on it. Before running `/handover` to exercise a change you
just made here, reinstall the global skill (`npx skills add
LorcanChinnock/handover -g` again, or copy `SKILL.md` directly into
`~/.claude/skills/handover/`) — otherwise you're silently testing stale
logic.

## Releases

`release-please` watches `main` and opens/updates a release PR from
conventional commit messages (`.github/workflows/release-please.yml`).
Merging that PR cuts the release and rewrites `CHANGELOG.md` — don't hand-edit
version numbers or the changelog outside of it.
