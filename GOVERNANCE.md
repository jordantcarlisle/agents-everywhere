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
