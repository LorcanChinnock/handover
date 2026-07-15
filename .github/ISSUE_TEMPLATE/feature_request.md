---
name: Feature request
about: Propose a change to how handover distills or expands
title: ""
labels: enhancement
---

**Problem**
What situation does the current behavior handle poorly?

**Proposed change**
What should `handover` do differently? Be specific about which mode
(Distill or Expand) and what part of the output changes — "it should handle
X better" is hard to review, "it should ask about X before writing the
Background section when Y" is easy to review.

**Why this keeps the output self-contained**
`handover`'s entire value is that a stranger with no shared history can act
on the prompt (see CONTRIBUTING.md). If this proposal adds a section, a mode,
or an assumption, make the case for why it's worth that cost.
