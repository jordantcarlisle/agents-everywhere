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
