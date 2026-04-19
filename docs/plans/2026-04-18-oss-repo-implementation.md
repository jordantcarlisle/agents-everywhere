# Community OSS Repo Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Scaffold `agentic-nwa/agents-everywhere` into a community OSS repo with a knowledge base, RFP system, agent-readable onboarding, and lazy-majority governance — per the design at `docs/specs/2026-04-18-oss-repo-design.md`.

**Architecture:** Pure markdown/YAML scaffolding with two small GitHub Actions workflows. Agent-readable entrypoints (`AGENTS.md`, `.claude/skills/`, `.cursor/rules/`, `.github/copilot-instructions.md`) orchestrate a three-flow contribution model (propose / document / vote). Governance is enforced by a path-scoped `lazy-majority-check` workflow + GitHub branch protection.

**Tech Stack:** Markdown, YAML, GitHub Actions (`actions/github-script@v7` inline JS + `actions/setup-python@v5` for the KB indexer), GitHub API via `gh` CLI.

---

## File Structure

**Created:**

```
AGENTS.md
CLAUDE.md
CONTRIBUTING.md
GOVERNANCE.md
CODE_OF_CONDUCT.md
proposals/TEMPLATE.md
proposals/0000-example.md
meetings/TEMPLATE.md
meetings/2026-04-18-kickoff/README.md
knowledge-base/index.md
.claude/skills/draft-proposal/SKILL.md
.claude/skills/write-meeting-notes/SKILL.md
.claude/skills/review-proposals/SKILL.md
.cursor/rules/agents-everywhere.mdc
.github/copilot-instructions.md
.github/CODEOWNERS
.github/DISCUSSION_TEMPLATE/idea.yml
.github/ISSUE_TEMPLATE/proposal-followup.yml
.github/ISSUE_TEMPLATE/meeting-note.yml
.github/ISSUE_TEMPLATE/bug.yml
.github/PULL_REQUEST_TEMPLATE/proposal.md
.github/pull_request_template.md
.github/workflows/lazy-majority.yml
.github/workflows/kb-index.yml
```

**Modified:**

```
README.md
```

**Operational (no files, gh api / gh commands):**

- Enable Discussions
- Create "Ideas" Discussion category
- Apply branch protection ruleset on `main`

---

## Task 1: Restructure README

**Files:**
- Modify: `README.md`

- [ ] **Step 1: Read current README to preserve tech stack + references**

Run: `cat README.md`

Preserve everything from `## Tech Stack` through end of file verbatim.

- [ ] **Step 2: Write new README with community intro above the preserved tech stack**

Replace the current file with the following content. The `<TECH_STACK_AND_REFERENCES_PRESERVED>` marker below means: paste the existing `## Tech Stack` section (including all subsections like "Search & Research", "IDEs", "Agent Harnesses", "Agent Skills & Plugins", "Design & Creative", "Search & Knowledge") and the `## References` section (Books, People, Initiatives) exactly as they are today — do not edit them.

```md
# Agents Everywhere All at Once

A Northwest Arkansas community of builders actively shipping AI agents.
This repo is our knowledge base, our RFP system, and the home of whatever
open-source project we decide to build together.

---

## 👉 New here? Hand this repo to your agent.

Clone the repo and open it in Claude Code, Cursor, Codex, or OpenClaw —
the agent will read `AGENTS.md` and walk you through the community and
how to contribute.

```
git clone https://github.com/agentic-nwa/agents-everywhere
cd agents-everywhere
claude   # or: cursor . / codex / openclaw
```

---

## What this is

Everyone's talking about agents. We're a room full of people actually
building with them.

Hosted out of Arkade in downtown Bentonville, our meetings are a 50/50
hybrid:

- **Half hackathon** — bring the agent project you're actively working on
  and push it forward with other builders in the room.
- **Half unconference** — volunteer-led workshops taught by whoever's
  learned something worth passing on. No keynote marathon. No vendor circus.

Arkansas ranks 47th nationally in agentic tool adoption. We think that's
less a problem than an opening — NWA has the builders, the operators, and
the capital to ship real agent products from here. This is the room for
the people doing it.

## Who it's for

Developers, operators, and founders in Northwest Arkansas who are
actively building with AI agents — professionally, on the side, or deep
in their first one. Bring your laptop, bring your project, come ready to
share what you've learned.

## How this repo works

Three things live here:

1. **Knowledge base** — notes, slides, and demos from every meeting, in
   `/meetings/YYYY-MM-DD-<slug>/`. Tagged so you can browse by topic too.
2. **Request for Proposals** — early ideas get kicked around in
   [Discussions](../../discussions). Formal RFPs are markdown files in
   `/proposals/`, submitted as PRs. Lazy majority (3 approvals, 7-day
   block window) accepts a proposal.
3. **The community project** — once the group picks an RFP, the code
   either lives here or spins out into its own repo. Proposer decides.

See [`CONTRIBUTING.md`](CONTRIBUTING.md) for how to submit a proposal,
contribute meeting notes, or vote. See [`GOVERNANCE.md`](GOVERNANCE.md)
for the decision rules.

## Volunteers

Kat and Jordan Carlisle jump-started this; it runs on whoever shows up.
Want to lead a session, a working group, or the next meeting? Just
start — open an issue or Discussion.

<TECH_STACK_AND_REFERENCES_PRESERVED>
```

- [ ] **Step 3: Verify preserved sections are intact**

Run: `grep -c "^## Tech Stack" README.md && grep -c "^## References" README.md && grep -c "^### Agent Harnesses" README.md`

Expected: `1` on each line.

- [ ] **Step 4: Commit**

```bash
git add README.md
git commit -m "Restructure README with community intro and agent-onboarding callout"
```

---

## Task 2: Add AGENTS.md and CLAUDE.md

**Files:**
- Create: `AGENTS.md`
- Create: `CLAUDE.md`

- [ ] **Step 1: Write AGENTS.md**

```md
# Instructions for agents working in this repo

You are helping a member of the Agents Everywhere community — an NWA-based
group of builders actively shipping AI agents. This repo is three things
at once: a knowledge base, an RFP system, and the home of whatever
open-source project the community decides to build together.

## Your job right now

The human just cloned this repo or pointed you at it. Before doing
anything else, figure out what they want to do. Ask them which of these
fits — do not guess:

1. **Draft a proposal** — they have an idea for what the community should
   build. If you have access to a `draft-proposal` skill, use it. Otherwise
   read `proposals/TEMPLATE.md` and `proposals/0000-example.md` and walk
   them through the template one question at a time.

2. **Write meeting notes** — they attended a session and want to
   contribute notes. If you have access to a `write-meeting-notes` skill,
   use it. Otherwise read `meetings/TEMPLATE.md`, create
   `meetings/YYYY-MM-DD-<slug>/README.md`, and help them fill it in with
   frontmatter tags.

3. **Review open proposals** — they want to vote. If you have access to a
   `review-proposals` skill, use it. Otherwise: list open PRs that touch
   `/proposals/`, summarize each, explain the lazy-majority rule (≥3
   approvals + 7-day block window), and show them how to 👍 or leave a
   blocking review with a reason.

4. **Understand the community** — they're new. Walk them through
   `README.md`, then `GOVERNANCE.md`, then offer the three options above.

If their request doesn't match, ask what they want to do.

## Ground rules

- Never push to `main`. All work goes through PRs.
- Proposals go in `/proposals/`, meeting notes go in
  `/meetings/YYYY-MM-DD-<slug>/`.
- Tag meeting notes so the KB index can roll them up.
- Follow `CONTRIBUTING.md` for commit style and PR etiquette.
- When filling in a template, read the `<!-- AGENT: Ask — ... -->`
  comments in each section. Those are your question prompts. Ask one at
  a time; fill in the answers as you go.

## Lazy-majority rule (for proposals only)

A proposal PR merges when:
- ≥3 approvals, AND
- No unresolved blocking reviews, AND
- PR has been open ≥7 days.

All three must hold. The `lazy-majority-check` GitHub Action enforces
this mechanically — you can't push past it.
```

- [ ] **Step 2: Write CLAUDE.md shim**

```md
See [AGENTS.md](AGENTS.md).
```

- [ ] **Step 3: Commit**

```bash
git add AGENTS.md CLAUDE.md
git commit -m "Add AGENTS.md agent entrypoint + CLAUDE.md shim"
```

---

## Task 3: Add GOVERNANCE.md

**Files:**
- Create: `GOVERNANCE.md`

- [ ] **Step 1: Write GOVERNANCE.md**

```md
# Governance

The rules, kept short on purpose.

## Decisions

We use **lazy majority** for proposals:

- A proposal PR merges when it has **≥3 approvals** and **no unresolved
  blocking objections for 7 consecutive days**.
- Anyone with a GitHub account can approve or block.
- A block must come with a reason. Blocks without reasons are ignored
  by maintainers.
- Maintainers do the actual merge. They don't get extra vote weight.

Meeting notes, demos, and knowledge-base contributions don't require
lazy majority. A single maintainer review is enough.

## Branch protection

- `main` requires a pull request.
- `main` requires 1 approval by default.
- `/proposals/**` PRs additionally require the `lazy-majority-check`
  status check to pass — which enforces the three conditions above.
- Force pushes and deletions are blocked.
- Conversation resolution is required before merge.

## Maintainers

Maintainers keep the repo moving — reviewing PRs, merging when the rule
is met, keeping spam out, and rotating the `CODEOWNERS` list. They're
not gatekeepers and they don't set direction.

Current maintainers are listed in `.github/CODEOWNERS`.

## Becoming a maintainer

If you've shipped a few PRs (a proposal, meeting notes, a demo — any
mix) and you want to help keep things moving, open an Issue saying so.
Existing maintainers add you unless someone blocks with a reason.

Same rule in reverse: maintainers who haven't been active for 3 months
rotate off, no drama.

## Changing these rules

This doc is itself a proposal when you want to change it. Open a PR
against `GOVERNANCE.md`, lazy-majority applies.
```

- [ ] **Step 2: Commit**

```bash
git add GOVERNANCE.md
git commit -m "Add GOVERNANCE.md with lazy-majority decision rule"
```

---

## Task 4: Add CONTRIBUTING.md

**Files:**
- Create: `CONTRIBUTING.md`

- [ ] **Step 1: Write CONTRIBUTING.md**

```md
# Contributing

Three ways to contribute. All of them are optimized for agents — open the
repo in Claude Code, Cursor, Codex, or OpenClaw and it will walk you
through any of these.

## 1. Propose something to build

Have an idea for what the community should build together?

- Toss it in [Discussions](../../discussions) to test the waters, **or**
- Jump straight to a formal RFP: ask your agent to run the
  `draft-proposal` flow (or read `proposals/TEMPLATE.md` and
  `proposals/0000-example.md`).

Submit as a PR against `/proposals/`. Lazy majority accepts it
(≥3 approvals + 7-day block window). See `GOVERNANCE.md` for the rule.

## 2. Write up a meeting

Attended a session? Help the community remember it.

- Ask your agent to run the `write-meeting-notes` flow, **or**
- Copy `meetings/TEMPLATE.md` into a new `meetings/YYYY-MM-DD-<slug>/`
  folder and fill it in.

Rough same-week notes beat polished never-notes. Ship it.

## 3. Vote on open proposals

Open the Pull Requests tab, filter by `proposals/`, read, and leave a 👍
or a blocking review with a reason. Or ask your agent to run the
`review-proposals` flow and walk you through what's open.

## House rules

- PRs only — nobody pushes to `main`.
- Be kind. See `CODE_OF_CONDUCT.md`.
- Tag meeting notes so the knowledge base index can roll them up.
- When in doubt, ask in Discussions.
```

- [ ] **Step 2: Commit**

```bash
git add CONTRIBUTING.md
git commit -m "Add CONTRIBUTING.md with three agent-friendly flows"
```

---

## Task 5: Add CODE_OF_CONDUCT.md

**Files:**
- Create: `CODE_OF_CONDUCT.md`

- [ ] **Step 1: Fetch Contributor Covenant 2.1 verbatim**

Run:
```bash
curl -sSL https://raw.githubusercontent.com/EthicalSource/contributor_covenant/release/content/version/2/1/code_of_conduct.md -o CODE_OF_CONDUCT.md
```

- [ ] **Step 2: Verify content was fetched**

Run: `head -3 CODE_OF_CONDUCT.md`

Expected: file starts with `# Contributor Covenant Code of Conduct`

- [ ] **Step 3: Set the enforcement contact**

Edit `CODE_OF_CONDUCT.md`: find the placeholder `[INSERT CONTACT METHOD]` and replace with `me@jordancarlisle.com`.

- [ ] **Step 4: Commit**

```bash
git add CODE_OF_CONDUCT.md
git commit -m "Add Contributor Covenant 2.1 Code of Conduct"
```

---

## Task 6: Create proposals/TEMPLATE.md and the worked example

**Files:**
- Create: `proposals/TEMPLATE.md`
- Create: `proposals/0000-example.md`

- [ ] **Step 1: Write proposals/TEMPLATE.md**

```md
---
title: <one-liner>
author: <github handle>
scope: in-repo | new-repo
status: draft
---

<!--
AGENT INSTRUCTIONS
If you're helping a community member fill this out, do NOT make them
stare at a blank file. Walk them through one question at a time using
the prompts below. Fill in their answers as you go, then read it back
and ask for edits.

Tone: plain, builder-to-builder. Not a grant application.
Length: fits on one screen, ~200–400 words.
-->

## The idea
<!-- AGENT: Ask — "In one or two sentences, what is this?" -->

## Why
<!-- AGENT: Ask — "Who benefits, and why now?" Keep it tight. -->

## Rough shape
<!-- AGENT: Ask — "What would this actually look like? Architecture,
     UX, a paragraph." Offer a diagram if it would help. -->

## In-repo or new repo?
<!-- AGENT: Ask — "Small tool/notebook/shared lib (in-repo) or
     standalone product (new repo)?" -->
- [ ] in-repo
- [ ] new-repo

## Who's in
<!-- AGENT: Ask — "Who's committing to work on this? Names, rough hours.
     'Just me' is a fine answer — just say it." -->

## Open questions
<!-- AGENT: Ask — "What don't you know yet? Where do you need help?" -->
```

- [ ] **Step 2: Write proposals/0000-example.md**

```md
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
```

- [ ] **Step 3: Commit**

```bash
git add proposals/TEMPLATE.md proposals/0000-example.md
git commit -m "Add proposals template (agent-walkable) and worked example"
```

---

## Task 7: Create meetings/TEMPLATE.md and kickoff seed

**Files:**
- Create: `meetings/TEMPLATE.md`
- Create: `meetings/2026-04-18-kickoff/README.md`

- [ ] **Step 1: Write meetings/TEMPLATE.md**

```md
---
date: YYYY-MM-DD
title: <short, memorable>
tags: []
attendees: <optional — count or vibe is fine>
---

<!--
AGENT INSTRUCTIONS
Help the human write up what happened at the meeting. Ask the questions
below one at a time. Keep it informal — these are field notes, not
conference proceedings.

Prompt for tags at the END, not the start — easier to tag once the
notes exist. Suggest tags based on what they told you.
-->

## What happened
<!-- AGENT: Ask — "In a few lines, what was the overall vibe and shape
     of the day?" -->

## Workshops & talks
<!-- AGENT: Ask — "Who led something, and what was it about?" List them
     with a one-line summary each. Link slides/demos if they're in
     ./slides or ./demos. -->

## Demos
<!-- AGENT: Ask — "Anyone demo a project? What and what stood out?" -->

## Links & resources
<!-- AGENT: Ask — "Any tools, papers, repos, or threads that got
     shared?" Bullet list. -->

## Follow-ups
<!-- AGENT: Ask — "Anything the group wants to pick up next time, or
     someone volunteered to do?" These can become Issues later. -->
```

- [ ] **Step 2: Write meetings/2026-04-18-kickoff/README.md (seed entry)**

```md
---
date: 2026-04-18
title: Agents Everywhere All at Once — Kickoff
tags: [kickoff, community, hackathon, unconference]
attendees: NWA builders at Arkade, Bentonville
---

## What happened

Inaugural meetup of the Agents Everywhere community. Half hackathon,
half unconference. Members brought agent projects in progress and shared
what they've been learning.

## Workshops & talks

_To be filled in by attendees — open a PR adding what you led or saw._

## Demos

_To be filled in by attendees._

## Links & resources

- [Arkade](https://arkade.com) — host venue
- This repo — `agentic-nwa/agents-everywhere`

## Follow-ups

- Collect workshop recaps from volunteers
- Fill in demo list
- Kick off first RFP thread in Discussions
```

- [ ] **Step 3: Commit**

```bash
git add meetings/TEMPLATE.md meetings/2026-04-18-kickoff/README.md
git commit -m "Add meetings template and kickoff seed entry"
```

---

## Task 8: Seed knowledge-base/index.md stub

**Files:**
- Create: `knowledge-base/index.md`

- [ ] **Step 1: Write the stub**

```md
<!--
This file is auto-generated by .github/workflows/kb-index.yml on every
push to main that touches meetings/**. Do not edit by hand; your changes
will be overwritten.

If this file is empty or out of date, trigger the workflow manually:
  gh workflow run kb-index.yml
-->

# Knowledge base (by tag)

_No tagged meetings yet. The first run of `kb-index.yml` after a meeting
note is merged will populate this file._
```

- [ ] **Step 2: Commit**

```bash
git add knowledge-base/index.md
git commit -m "Seed knowledge-base/index.md stub (auto-generated by CI)"
```

---

## Task 9: Create .claude/skills

**Files:**
- Create: `.claude/skills/draft-proposal/SKILL.md`
- Create: `.claude/skills/write-meeting-notes/SKILL.md`
- Create: `.claude/skills/review-proposals/SKILL.md`

- [ ] **Step 1: Write .claude/skills/draft-proposal/SKILL.md**

```md
---
name: draft-proposal
description: Use when a community member wants to propose something for Agents Everywhere to build. Walks them through the RFP template one question at a time and opens a PR.
---

# draft-proposal

When the user wants to propose something to the community:

1. Read `proposals/TEMPLATE.md` and `proposals/0000-example.md`.
2. Ask the 6 questions in the template, ONE at a time. Don't batch.
   - "In one or two sentences, what is this?"
   - "Who benefits, and why now?"
   - "What would this actually look like — architecture, UX, a paragraph?"
   - "Small tool/notebook/shared lib (in-repo) or standalone product (new repo)?"
   - "Who's committing to work on this? Names, rough hours. 'Just me' is fine."
   - "What don't you know yet? Where do you need help?"
3. Help them pick a short slug for the filename (kebab-case, 2–4 words).
4. Write the draft to `proposals/YYYY-MM-DD-<slug>.md` using today's date
   and their answers. Fill in frontmatter (`title`, `author`, `scope`,
   `status: draft`).
5. Read it back to them. Ask if they want to tighten anything.
6. Offer two paths:
   a) Post to [Discussions](../../discussions) first to gauge interest, then PR.
   b) Go straight to PR if they already have committed collaborators.
7. If (b), create a branch, commit, push, and open the PR with
   `gh pr create`. The proposal PR template will apply automatically.
8. Remind them of the lazy-majority rule: ≥3 approvals + 7-day block
   window = accepted. The `lazy-majority-check` CI check will track it.
```

- [ ] **Step 2: Write .claude/skills/write-meeting-notes/SKILL.md**

```md
---
name: write-meeting-notes
description: Use when a community member wants to write up what happened at an Agents Everywhere meeting. Creates a dated meeting folder and walks them through the template.
---

# write-meeting-notes

When the user wants to write up a meeting:

1. Read `meetings/TEMPLATE.md`.
2. Ask for the meeting date (YYYY-MM-DD) and a short title.
3. Pick a slug (kebab-case) and create the folder
   `meetings/YYYY-MM-DD-<slug>/`. Copy `meetings/TEMPLATE.md` to
   `meetings/YYYY-MM-DD-<slug>/README.md`.
4. Walk them through the 5 questions one at a time:
   - "In a few lines, what was the overall vibe and shape of the day?"
   - "Who led something, and what was it about?"
   - "Anyone demo a project? What and what stood out?"
   - "Any tools, papers, repos, or threads that got shared?"
   - "Anything the group wants to pick up next time?"
5. Fill in their answers as you go.
6. At the end, suggest tags based on what got discussed. Confirm with
   them, then write the tag list into the frontmatter `tags: [...]`.
7. If they mention demos or slides, create `./demos/` and `./slides/`
   subfolders and remind them to drop materials in.
8. Open a PR. Meeting notes don't need lazy-majority acceptance — a
   single maintainer review is enough to merge.
```

- [ ] **Step 3: Write .claude/skills/review-proposals/SKILL.md**

```md
---
name: review-proposals
description: Use when a community member wants to review and vote on open RFPs. Lists open proposals, summarizes each, and walks them through voting.
---

# review-proposals

When the user wants to review open proposals:

1. Run: `gh pr list --repo agentic-nwa/agents-everywhere --state open --search "files:proposals/"`.
2. For each open PR, read the proposal file on the PR branch:
   `gh pr view <number> --json files,body,title,createdAt,reviews`.
3. Give the user a one-paragraph summary of each proposal: what it is,
   why the author says it matters, who's committing to build it.
4. For each, show:
   - Current approval count
   - Any blocking reviews (with reasons)
   - Days since the PR was opened (and days remaining in the 7-day window)
5. Ask the user which they want to weigh in on.
6. For their choice, help them either:
   - Approve: `gh pr review <number> --approve -b "<reason>"`
   - Block: `gh pr review <number> --request-changes -b "<reason>"`
     (require a reason — blocks without reasons are ignored)
   - Comment: `gh pr review <number> --comment -b "<text>"`
7. After they vote, remind them of the lazy-majority rule: ≥3 approvals
   + no unresolved blocks + 7 days open = merge.
```

- [ ] **Step 4: Commit**

```bash
git add .claude/skills/
git commit -m "Add Claude Code skills for the three community flows"
```

---

## Task 10: Cursor + Copilot agent configs

**Files:**
- Create: `.cursor/rules/agents-everywhere.mdc`
- Create: `.github/copilot-instructions.md`

Both files hold the same content as `AGENTS.md`'s body, in the format each tool expects. Keep in sync with `AGENTS.md`; the spec notes this and flags drift as an open question for a future iteration.

- [ ] **Step 1: Write .cursor/rules/agents-everywhere.mdc**

```md
---
description: Agents Everywhere community repo — onboarding and contribution flow.
globs: ["**/*"]
alwaysApply: true
---

You are helping a member of the Agents Everywhere community — an NWA-based
group of builders actively shipping AI agents. This repo is three things
at once: a knowledge base, an RFP system, and the home of whatever
open-source project the community decides to build together.

Before doing anything else, ask the user which of these they want to do:

1. Draft a proposal — read `proposals/TEMPLATE.md` and `proposals/0000-example.md`, walk through the template one question at a time.
2. Write meeting notes — read `meetings/TEMPLATE.md`, create `meetings/YYYY-MM-DD-<slug>/README.md`, walk through the template.
3. Review open proposals — list PRs touching `/proposals/`, summarize, explain the lazy-majority rule, help them vote.
4. Understand the community — walk through `README.md`, then `GOVERNANCE.md`, then offer the three options above.

Ground rules:
- Never push to `main`. All work goes through PRs.
- Proposals in `/proposals/`, meeting notes in `/meetings/YYYY-MM-DD-<slug>/`.
- Tag meeting notes (frontmatter `tags: [...]`).
- Follow `CONTRIBUTING.md` for commit style.
- Respect `<!-- AGENT: Ask — ... -->` comments in templates — one question per section, asked one at a time.

Lazy-majority rule (proposals only): PR merges when ≥3 approvals AND no unresolved blocking reviews AND PR age ≥7 days. Enforced by the `lazy-majority-check` GitHub Action.
```

- [ ] **Step 2: Write .github/copilot-instructions.md**

```md
# Copilot instructions for agents-everywhere

You are helping a member of the Agents Everywhere community — an NWA
group of builders shipping AI agents. This repo is a knowledge base, an
RFP system, and the home of whatever project the community builds.

Before doing anything else, ask the user which they want to do:

1. Draft a proposal (read `proposals/TEMPLATE.md` + `proposals/0000-example.md`)
2. Write meeting notes (read `meetings/TEMPLATE.md`)
3. Review open proposals (list PRs touching `/proposals/`)
4. Understand the community (walk `README.md` → `GOVERNANCE.md`)

Ground rules:
- PRs only; never push to `main`.
- Proposals live in `/proposals/`, meeting notes in `/meetings/YYYY-MM-DD-<slug>/`.
- Tag meeting notes so the KB index can roll them up.
- Templates have `<!-- AGENT: Ask — ... -->` comments — treat each as one question; ask one at a time.

Lazy-majority rule (proposals only): PR merges at ≥3 approvals, no unresolved blocks, ≥7 days open. Enforced by `lazy-majority-check` CI.
```

- [ ] **Step 3: Commit**

```bash
git add .cursor/rules/ .github/copilot-instructions.md
git commit -m "Add Cursor rules and Copilot instructions mirroring AGENTS.md"
```

---

## Task 11: CODEOWNERS + Discussion/Issue/PR templates

**Files:**
- Create: `.github/CODEOWNERS`
- Create: `.github/DISCUSSION_TEMPLATE/idea.yml`
- Create: `.github/ISSUE_TEMPLATE/proposal-followup.yml`
- Create: `.github/ISSUE_TEMPLATE/meeting-note.yml`
- Create: `.github/ISSUE_TEMPLATE/bug.yml`
- Create: `.github/PULL_REQUEST_TEMPLATE/proposal.md`
- Create: `.github/pull_request_template.md`

- [ ] **Step 1: Write .github/CODEOWNERS**

```
# Default owners — auto-requested for review on every PR.
# Grow this list as volunteer maintainers step up (see GOVERNANCE.md).

* @jordantcarlisle
```

(Note: add @katcarlisle once her GitHub handle is confirmed. One PR to update this file is the right way.)

- [ ] **Step 2: Write .github/DISCUSSION_TEMPLATE/idea.yml**

```yaml
title: "[Idea] "
labels: ["idea"]
body:
  - type: markdown
    attributes:
      value: |
        Kicking around an idea for what the community should build? Low
        friction here. If it gets traction, formalize it as a PR against
        /proposals/ (or ask your agent to run `draft-proposal`).
  - type: input
    id: one-liner
    attributes:
      label: What's the idea in one line?
    validations:
      required: true
  - type: textarea
    id: why
    attributes:
      label: Why does the community want this?
      description: Who benefits, why now.
    validations:
      required: true
  - type: textarea
    id: shape
    attributes:
      label: What would the rough shape look like?
      description: Architecture, UX, a paragraph. No need to over-spec.
  - type: input
    id: who
    attributes:
      label: Who do you think would build it?
```

- [ ] **Step 3: Write .github/ISSUE_TEMPLATE/proposal-followup.yml**

```yaml
name: Proposal follow-up
description: Track implementation of a merged proposal.
title: "[Proposal follow-up] "
labels: ["proposal-followup"]
body:
  - type: input
    id: proposal
    attributes:
      label: Which merged proposal does this track?
      description: Link to the proposal file (e.g. `proposals/2026-05-01-foo.md`) or the merged PR.
    validations:
      required: true
  - type: dropdown
    id: scope
    attributes:
      label: Scope
      options:
        - in-repo
        - new-repo
    validations:
      required: true
  - type: input
    id: repo
    attributes:
      label: If new-repo, link to it
  - type: textarea
    id: next
    attributes:
      label: What's next?
      description: What needs to happen to move this forward. Break it down if helpful.
```

- [ ] **Step 4: Write .github/ISSUE_TEMPLATE/meeting-note.yml**

```yaml
name: Meeting note
description: I want to write up what happened at a meeting.
title: "[Meeting note] "
labels: ["meeting-note"]
body:
  - type: markdown
    attributes:
      value: |
        Prefer to just open a PR directly? Go for it — copy
        `meetings/TEMPLATE.md` into a new `meetings/YYYY-MM-DD-<slug>/`
        folder. This Issue is for when you want to claim the writeup
        publicly first.
  - type: input
    id: date
    attributes:
      label: Meeting date (YYYY-MM-DD)
    validations:
      required: true
  - type: textarea
    id: plan
    attributes:
      label: What will you cover?
```

- [ ] **Step 5: Write .github/ISSUE_TEMPLATE/bug.yml**

```yaml
name: Bug
description: Something in the repo itself is broken.
title: "[Bug] "
labels: ["bug"]
body:
  - type: textarea
    id: what
    attributes:
      label: What's broken?
    validations:
      required: true
  - type: textarea
    id: repro
    attributes:
      label: How to reproduce
  - type: textarea
    id: expected
    attributes:
      label: What should happen
```

- [ ] **Step 6: Write .github/PULL_REQUEST_TEMPLATE/proposal.md**

```md
## Proposal: <title>

<!-- AGENT: Confirm these before the human submits. Don't ask them to
     tick boxes — just verify the file yourself. -->

- [ ] `proposals/TEMPLATE.md` filled out
- [ ] Discussions thread linked (if there was one)
- [ ] Scope declared (in-repo / new-repo)
- [ ] Someone named under "Who's in"

<!-- Lazy majority: ≥3 approvals + no unresolved blocks + 7 days open = merge.
     The lazy-majority-check CI gate enforces this mechanically. -->
```

- [ ] **Step 7: Write .github/pull_request_template.md (default)**

```md
## Summary

<!-- What does this PR do? Keep it short. -->

## For meeting notes

- [ ] Frontmatter `tags: [...]` is filled in so the KB index can roll it up.

## For proposals

- See `.github/PULL_REQUEST_TEMPLATE/proposal.md` — GitHub will use that
  template automatically if your PR touches `/proposals/`. If it didn't,
  apply it manually.
```

Note on GitHub's behavior: GitHub uses `.github/pull_request_template.md` as the default. To apply `proposal.md` automatically, authors include `?template=proposal.md` in the PR URL, or `.claude/skills/draft-proposal/SKILL.md` does it for them. We'll rely on the skill.

- [ ] **Step 8: Commit**

```bash
git add .github/CODEOWNERS .github/DISCUSSION_TEMPLATE/ .github/ISSUE_TEMPLATE/ .github/PULL_REQUEST_TEMPLATE/ .github/pull_request_template.md
git commit -m "Add CODEOWNERS, Discussion/Issue/PR templates"
```

---

## Task 12: Lazy-majority GitHub Action

**Files:**
- Create: `.github/workflows/lazy-majority.yml`

This workflow is the mechanical enforcer of the lazy-majority rule. It runs on every PR event, decides whether the PR touches `/proposals/`, and posts a commit status named `lazy-majority-check` that's `success` only when all three conditions hold.

- [ ] **Step 1: Write the workflow**

```yaml
name: lazy-majority-check

on:
  pull_request:
    types: [opened, synchronize, reopened, ready_for_review]
  pull_request_review:
    types: [submitted, dismissed]

permissions:
  contents: read
  pull-requests: read
  statuses: write

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - name: Evaluate lazy-majority conditions
        uses: actions/github-script@v7
        with:
          script: |
            const pr = context.payload.pull_request;
            const { owner, repo } = context.repo;
            const sha = pr.head.sha;

            // 1. Does this PR touch /proposals/?
            const files = await github.paginate(
              github.rest.pulls.listFiles,
              { owner, repo, pull_number: pr.number, per_page: 100 }
            );
            const touchesProposals = files.some(f =>
              f.filename.startsWith('proposals/')
            );

            if (!touchesProposals) {
              await github.rest.repos.createCommitStatus({
                owner, repo, sha,
                state: 'success',
                context: 'lazy-majority-check',
                description: 'Not a proposal PR — skipping lazy-majority rule.'
              });
              return;
            }

            // 2. Count distinct-approver approvals and unresolved blocks.
            const reviews = await github.paginate(
              github.rest.pulls.listReviews,
              { owner, repo, pull_number: pr.number, per_page: 100 }
            );
            const latestByReviewer = new Map();
            for (const r of reviews) {
              if (!r.user) continue;
              // Track only APPROVED / CHANGES_REQUESTED / DISMISSED.
              if (!['APPROVED', 'CHANGES_REQUESTED', 'DISMISSED'].includes(r.state)) continue;
              latestByReviewer.set(r.user.login, r.state);
            }
            const approvals = [...latestByReviewer.values()]
              .filter(s => s === 'APPROVED').length;
            const blocks = [...latestByReviewer.entries()]
              .filter(([, s]) => s === 'CHANGES_REQUESTED');

            // 3. PR age in days.
            const ageMs = Date.now() - new Date(pr.created_at).getTime();
            const ageDays = ageMs / (1000 * 60 * 60 * 24);

            // Evaluate.
            const needed = 3;
            const windowDays = 7;
            const remainingApprovals = Math.max(0, needed - approvals);
            const remainingDays = Math.max(0, windowDays - ageDays);

            let state = 'success';
            let description = `OK: ${approvals} approvals, 0 blocks, ${ageDays.toFixed(1)}d open.`;

            if (blocks.length > 0) {
              state = 'failure';
              const names = blocks.map(([u]) => '@' + u).join(', ');
              description = `Blocked by ${names}. Resolve before merge.`;
            } else if (remainingApprovals > 0) {
              state = 'failure';
              description = `Needs ${remainingApprovals} more approval(s) (have ${approvals}/3).`;
            } else if (remainingDays > 0) {
              state = 'failure';
              description = `7-day window: ${remainingDays.toFixed(1)} day(s) remaining.`;
            }

            // GitHub status description max is 140 chars.
            if (description.length > 140) description = description.slice(0, 137) + '...';

            await github.rest.repos.createCommitStatus({
              owner, repo, sha,
              state,
              context: 'lazy-majority-check',
              description
            });
```

- [ ] **Step 2: Lint the YAML**

Run: `python3 -c "import yaml; yaml.safe_load(open('.github/workflows/lazy-majority.yml'))"`

Expected: no output (exit 0). If it errors, fix indentation.

- [ ] **Step 3: Commit**

```bash
git add .github/workflows/lazy-majority.yml
git commit -m "Add lazy-majority-check workflow (path-scoped to proposals/)"
```

- [ ] **Step 4: Note for post-merge**

After all commits are pushed, this workflow will trigger on subsequent PRs. First run happens the moment someone opens a PR against `main`. Verify behavior on the first real proposal PR or on a throwaway test PR.

---

## Task 13: Knowledge-base indexer workflow

**Files:**
- Create: `.github/workflows/kb-index.yml`

Rebuilds `knowledge-base/index.md` by grouping meetings by their frontmatter tags. Runs on pushes to `main` that touch `meetings/**`. Commits the regenerated file as `github-actions[bot]`.

- [ ] **Step 1: Write the workflow**

```yaml
name: kb-index

on:
  push:
    branches: [main]
    paths:
      - 'meetings/**'

permissions:
  contents: write

jobs:
  rebuild:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Install deps
        run: pip install pyyaml

      - name: Rebuild knowledge-base/index.md
        run: |
          python3 <<'PY'
          import os, re, yaml
          from collections import defaultdict
          from pathlib import Path

          root = Path('.')
          meetings_dir = root / 'meetings'
          out_path = root / 'knowledge-base' / 'index.md'

          entries_by_tag = defaultdict(list)

          for meeting in sorted(meetings_dir.iterdir()):
              if not meeting.is_dir():
                  continue
              readme = meeting / 'README.md'
              if not readme.exists():
                  continue
              text = readme.read_text()
              m = re.match(r'^---\n(.*?)\n---\n', text, re.S)
              if not m:
                  continue
              fm = yaml.safe_load(m.group(1)) or {}
              date = str(fm.get('date', meeting.name[:10]))
              title = fm.get('title', meeting.name)
              tags = fm.get('tags', []) or []
              for tag in tags:
                  entries_by_tag[tag].append((date, title, meeting.name))

          lines = [
              '<!--',
              'This file is auto-generated by .github/workflows/kb-index.yml.',
              'Do not edit by hand; your changes will be overwritten.',
              '-->',
              '',
              '# Knowledge base (by tag)',
              '',
          ]
          if not entries_by_tag:
              lines.append('_No tagged meetings yet._')
          else:
              for tag in sorted(entries_by_tag):
                  lines.append(f'## #{tag}')
                  for date, title, slug in sorted(entries_by_tag[tag], reverse=True):
                      lines.append(f'- [{date} — {title}](../meetings/{slug}/)')
                  lines.append('')

          out_path.write_text('\n'.join(lines) + '\n')
          PY

      - name: Commit if changed
        run: |
          if ! git diff --quiet knowledge-base/index.md; then
            git config user.name "github-actions[bot]"
            git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
            git add knowledge-base/index.md
            git commit -m "chore(kb): regenerate knowledge-base/index.md"
            git push
          fi
```

- [ ] **Step 2: Lint the YAML**

Run: `python3 -c "import yaml; yaml.safe_load(open('.github/workflows/kb-index.yml'))"`

Expected: no output (exit 0).

- [ ] **Step 3: Commit**

```bash
git add .github/workflows/kb-index.yml
git commit -m "Add kb-index workflow to regenerate knowledge-base/index.md"
```

---

## Task 14: Push everything

- [ ] **Step 1: Push all commits to origin**

```bash
git push origin main
```

Expected: push succeeds. If it fails because `main` already has branch protection from an earlier configuration, push from a feature branch and open a PR — but at this point branch protection isn't yet configured, so direct push should work.

- [ ] **Step 2: Verify by listing files via gh**

```bash
gh api /repos/agentic-nwa/agents-everywhere/contents -q '.[].name' | sort
```

Expected: output includes `AGENTS.md`, `CLAUDE.md`, `CODE_OF_CONDUCT.md`, `CONTRIBUTING.md`, `GOVERNANCE.md`, `LICENSE`, `README.md`, `docs`, `knowledge-base`, `meetings`, `proposals`.

---

## Task 15: Enable Discussions + create Ideas category

- [ ] **Step 1: Enable Discussions on the repo**

```bash
gh api -X PATCH /repos/agentic-nwa/agents-everywhere -f has_discussions=true
```

Expected: JSON response includes `"has_discussions": true`.

- [ ] **Step 2: List current discussion categories**

```bash
gh api graphql -f query='
  query {
    repository(owner: "agentic-nwa", name: "agents-everywhere") {
      discussionCategories(first: 20) {
        nodes { id name slug }
      }
    }
  }'
```

Note whether an `Ideas` category already exists (GitHub seeds defaults when Discussions is first enabled). If yes, skip Step 3.

- [ ] **Step 3: If no Ideas category, create one**

```bash
gh api graphql -f query='
  mutation($repoId: ID!) {
    createDiscussionCategory(input: {
      repositoryId: $repoId
      name: "Ideas"
      emoji: ":bulb:"
      description: "Early-stage community ideas. If one gets traction, formalize as a PR against /proposals/."
      format: OPEN_DISCUSSION
    }) { category { id name } }
  }' -F repoId="$(gh api /repos/agentic-nwa/agents-everywhere --jq '.node_id')"
```

Note: `createDiscussionCategory` requires the repo owner's admin token. If this mutation is not available on free plans, fall back to creating the category manually in the GitHub UI: Settings → Features → Discussions → "Edit categories".

- [ ] **Step 4: Verify category exists**

```bash
gh api graphql -f query='
  query {
    repository(owner: "agentic-nwa", name: "agents-everywhere") {
      discussionCategories(first: 20) { nodes { name slug } }
    }
  }' | grep -i ideas
```

Expected: a line containing `"Ideas"`.

---

## Task 16: Apply branch protection ruleset

Baseline protection on `main` + a path-scoped rule requiring `lazy-majority-check` for `/proposals/**`.

- [ ] **Step 1: Apply baseline branch protection**

```bash
gh api -X PUT /repos/agentic-nwa/agents-everywhere/branches/main/protection \
  --input - <<'JSON'
{
  "required_status_checks": null,
  "enforce_admins": false,
  "required_pull_request_reviews": {
    "required_approving_review_count": 1,
    "dismiss_stale_reviews": true,
    "require_code_owner_reviews": false
  },
  "restrictions": null,
  "required_conversation_resolution": true,
  "allow_force_pushes": false,
  "allow_deletions": false
}
JSON
```

Expected: JSON response describing the applied protection.

- [ ] **Step 2: Add path-scoped ruleset for lazy-majority-check**

GitHub's branch protection API doesn't support path-scoped status-check requirements directly — that lives in the newer **rulesets** API. Apply a repo ruleset:

```bash
gh api -X POST /repos/agentic-nwa/agents-everywhere/rulesets \
  --input - <<'JSON'
{
  "name": "proposals-lazy-majority",
  "target": "branch",
  "enforcement": "active",
  "conditions": {
    "ref_name": { "include": ["refs/heads/main"], "exclude": [] }
  },
  "rules": [
    {
      "type": "pull_request",
      "parameters": {
        "required_approving_review_count": 3,
        "dismiss_stale_reviews_on_push": true,
        "require_code_owner_review": false,
        "require_last_push_approval": false,
        "required_review_thread_resolution": true
      }
    },
    {
      "type": "required_status_checks",
      "parameters": {
        "strict_required_status_checks_policy": false,
        "required_status_checks": [
          { "context": "lazy-majority-check" }
        ]
      }
    }
  ]
}
JSON
```

Note: GitHub rulesets don't currently expose per-path rules via REST — this ruleset applies to all PRs against `main`. Combined with the `lazy-majority-check` workflow (which short-circuits to `success` for non-proposal PRs), this is effectively path-scoped behavior. The 3-approval rule does apply globally, however — which is stricter than intended for meeting notes.

**If global 3-approval is too strict** (likely), remove the `pull_request` rule from the ruleset and rely solely on the `lazy-majority-check` gate. To do so, re-send the POST above with only the `required_status_checks` rule. Revisit after first meeting.

- [ ] **Step 3: Verify**

```bash
gh api /repos/agentic-nwa/agents-everywhere/branches/main/protection --jq '.required_pull_request_reviews'
gh api /repos/agentic-nwa/agents-everywhere/rulesets --jq '.[].name'
```

Expected: protection shows 1 required review; rulesets list includes `proposals-lazy-majority`.

---

## Task 17: Seed welcome Discussion

- [ ] **Step 1: Create the seed Discussion**

```bash
CATEGORY_ID=$(gh api graphql -f query='
  query {
    repository(owner: "agentic-nwa", name: "agents-everywhere") {
      discussionCategories(first: 20) { nodes { id name } }
    }
  }' --jq '.data.repository.discussionCategories.nodes[] | select(.name=="Ideas") | .id')

REPO_ID=$(gh api /repos/agentic-nwa/agents-everywhere --jq '.node_id')

gh api graphql -f query='
  mutation($repoId: ID!, $catId: ID!) {
    createDiscussion(input: {
      repositoryId: $repoId
      categoryId: $catId
      title: "Welcome — how this community works"
      body: "Hey builders — welcome to Agents Everywhere.\n\nThis Discussion is the low-friction place to toss ideas for what we should build together. If an idea gets traction here, formalize it as a PR against `/proposals/` (or ask your agent to run the `draft-proposal` skill). See `proposals/0000-example.md` for a worked example.\n\nThree ways to contribute (see `CONTRIBUTING.md`):\n1. Propose something → PR against `/proposals/`\n2. Write up a meeting → folder in `/meetings/YYYY-MM-DD-<slug>/`\n3. Vote on open proposals → approve or block on their PR\n\nDrop your first idea as a reply."
    }) { discussion { url } }
  }' -F repoId=$REPO_ID -F catId=$CATEGORY_ID --jq '.data.createDiscussion.discussion.url'
```

Expected: a URL to the new Discussion. Note the URL — you'll share it in the first blast email.

---

## Self-Review

**Spec coverage:**
- Repo layout (spec §Architecture): covered by Tasks 1–11.
- RFP flow (spec §Three flows, Flow 1): proposals template/example (Task 6), Claude skill (Task 9), PR template (Task 11), CI gate (Task 12), branch-protection hookup (Task 16).
- Meeting flow (Flow 2): meetings template/seed (Task 7), Claude skill (Task 9), KB indexer (Task 13).
- Review flow (Flow 3): review-proposals skill (Task 9).
- Agent-readable layer: AGENTS.md + CLAUDE.md (Task 2), skills (Task 9), Cursor + Copilot (Task 10).
- Governance: GOVERNANCE.md (Task 3), CONTRIBUTING (Task 4), CODE_OF_CONDUCT (Task 5), CODEOWNERS (Task 11), branch protection (Task 16).
- Content seeds: example proposal (Task 6), kickoff meeting (Task 7), KB stub (Task 8), welcome Discussion (Task 17).
- Operational migration: Tasks 14–17.

No spec gaps.

**Placeholder scan:** no "TBD", "TODO", "implement later", "fill in details", or "similar to Task N" references remain. Every code/config step contains the actual content.

**Type/naming consistency:**
- `lazy-majority-check` — used consistently as the status-check context in Tasks 12 and 16, and in `AGENTS.md`/`GOVERNANCE.md`.
- `draft-proposal`, `write-meeting-notes`, `review-proposals` — skill directory names and references match across Tasks 2, 9, 10, and the skill files.
- `agentic-nwa/agents-everywhere` — repo slug used consistently in commands, skills, and docs.
- Lazy-majority thresholds (3 approvals, 7-day window) — consistent across AGENTS.md, GOVERNANCE.md, CONTRIBUTING.md, the example PR template, the skill, and the Action script.

No inconsistencies found.

**Known intentional limitation:** The GitHub rulesets API doesn't currently support per-path PR rules; Task 16 Step 2 notes this explicitly and provides a fallback. Not a plan defect — a platform constraint called out for the implementer.
