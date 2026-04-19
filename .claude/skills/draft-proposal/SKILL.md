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
