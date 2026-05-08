---
title: Shared Agent Eval Harness
author: jordantcarlisle
scope: in-repo
status: draft
---

## The idea

Build a shared eval harness the community can use to run the same tasks
across the agent tools people are actually using — Claude Code, Codex,
Cursor, OpenCode, and similar local-first workflows.

## Why

Right now everybody is running private evals in slightly different ways,
which makes it hard to compare results, hard to share what works, and
hard for new members to contribute without rebuilding the same test rig
from scratch. A shared harness gives the group a concrete project with a
useful output: comparable runs, reusable task packs, and an artifact the
community can improve in public.

## Rough shape

Start as an in-repo project with:

- a small task format (prompt, expected output, scoring hints, tags)
- adapters for the first few agent workflows people in the room already use
- a CLI to run a task set against one or more harnesses
- JSON outputs plus a simple HTML or markdown summary for comparison
- a handful of seed tasks drawn from real builder workflows in the group

v1 should stay local-first and file-based. No hosted service required.
If the project grows beyond a lightweight shared tool, we can spin it out
later.

## Who's in

- @jordantcarlisle — maintainer/champion, initial framing + repo setup
- Looking for 2–3 additional contributors who actively use at least one
  of the target agent workflows and can help shape the first task set

## Open questions

- Which harnesses should be first-class in v1 versus handled later?
- Do we start with rule-based scoring only, or allow LLM-judge scoring
  behind an explicit opt-in?
- What is the smallest useful starter task pack for the community?
- How should we keep sensitive prompts or outputs out of the public repo?

## In-repo or new repo?
- [x] in-repo
- [ ] new-repo

Small enough to start here, easy to review in the open, and a good first
community project for testing the repo's proposal workflow.
