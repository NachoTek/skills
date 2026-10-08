---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

If the user passes a ticket reference, fetch it from the issue tracker and state its title before starting. If the reference is ambiguous, ask.

Use /tdd where possible, at pre-agreed seams.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, use /code-review to review the work.

Commit your work to the current branch.

## PR publication (GitHub lane: required, not optional)

When the ticket or spec says the work ships via PR (the NachoTek lane always does):

1. Resolve the integration base before creating the branch. Use the explicit campaign/feature branch named by the parent issue or coordinator; otherwise use the repository default branch. Record it as `BASE_BRANCH`.
2. Create one branch per ticket, named `<issue-number>-<slug>`, from the current `origin/$BASE_BRANCH`. Never commit or push directly to `main` or another integration base.
3. Push and open the PR with `--base "$BASE_BRANCH"`. The PR body MUST end with `Closes #<issue-number>`. Commit-title references like `(#98)` do not close issues.
4. Run `/code-review` and fix its findings before publication. CI requests `TerminalSausage` automatically when green; do not use legacy review labels or third-party review routes.
5. Post a completion comment on the ticket: base branch, branch, PR link, diff summary, and verification (test counts and lanes run).
6. At an external CI or human-review gate, return a receipt containing PR, base, branch, worktree, and head SHA, then end the turn. Do not sleep or poll. The coordinator records this child task ID and resumes this same session when Herdr delivers the event.
7. When resumed, verify GitHub ground truth. If the PR is behind, rebase on the current `origin/$BASE_BRANCH`, push with `--force-with-lease`, return a fresh receipt, and stop for the new CI/review event. If approved and mergeable, squash-merge, explicitly close the issue when a non-default integration base prevented auto-close, then remove local/remote branch and worktree residue.
8. Finish with a closure receipt: merged PR and SHA, issue state, deleted refs, removed worktree, and clean base checkout. The coordinator independently verifies it and updates the goal tracker.

For a campaign branch, child tickets use ticket branches and PRs targeting that campaign branch. Only the final campaign PR targets the default branch and closes the parent epic. Protect the campaign branch with the same required CI/review rules as `main`; it is an integration base, not a shared working tree.

## Scope discipline

- Stay inside the ticket boundaries: "blocked by" tickets are not started early; follow-up enhancements stay out unless the ticket says otherwise.
- If a fix belongs to a different ticket, note it in the PR body as out-of-scope rather than implementing it.
