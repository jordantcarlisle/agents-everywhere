---
title: Shared Agent Eval Harness
author: example-contributor
scope: in-repo
status: example
---

<!-- This is a worked example, not an accepted proposal.
     Remove this file or leave it — either way, new proposals should
     live in their own YYYY-MM-DD-<slug>.md files. -->

## The idea

A shared harness for evaluating agent runs across the tools our members
use — Claude Code, Codex, Cursor, OpenClaw. Same prompts, same scoring,
results tracked in one place.

## Why

Everybody in the community is running their own private evals. We
duplicate effort, can't compare apples to apples, and newcomers can't
tell which harness fits their problem. A shared harness makes us
collectively smarter about what actually works.

## Rough shape

A Python package in `/project/eval-harness/` with:

- A task file format (YAML: prompt, expected output, scoring hints).
- Adapters for each harness (subprocess calls + output parsing).
- A CLI: `eval run tasks/rag/ --harness claude-code`.
- Results as JSON; a small dashboard script renders to HTML.

Runs locally. No hosted service in v1.

## In-repo or new repo?
- [x] in-repo
- [ ] new-repo

Small enough to live here. If it grows into a product, spin out later.

## Who's in

- @example-contributor — 4 hrs/week
- Looking for one more person who has used Codex seriously.

## Open questions

- Do we version the task files or let them drift?
- Scoring: LLM-judge or rule-based? Probably start rule-based.
- How do we keep sensitive eval data out of the public repo?
