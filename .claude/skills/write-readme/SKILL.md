---
name: write-readme
description:
  Author or substantially revise README.md via the persona-review workflow
---

Invoke the `reviewed-writer:write-doc` skill: it reads this repository's
profile (`.claude/rules/persona-review-profile.md`), whose Document section
names `README.md`, and runs the authoring workflow — rubric, three diverse
drafts, persona panel, fact-check, synthesis, and re-review until every
persona ships. Pass along the change driving the revision and any design
brief or scoping the user supplied.
