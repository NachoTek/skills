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

1. Branch: one branch per ticket, named `<issue-number>-<slug>` off current main (e.g. `98-logging-foundation`).
2. Push and open a PR against main.
3. PR body MUST end with a closing keyword line so the parent issue auto-closes on merge:
   `Closes #<issue-number>`
   Commit-title references like `(#98)` do NOT auto-close; the keyword must be in the PR BODY. (Missed on PR #116; issue #98 had to be closed by hand. Do not repeat.)
4. Add the `ready-for-codereview` label to the PR: this fires the automated review webhook.
5. Post a completion comment on the ticket: branch, PR link, diff summary, verification (test counts, lanes run).
6. Delete the branch after merge (bot merges prune it; if not, clean it up on next touch).
7. Address automated-review findings by pushing fixes to the same branch; the review cycle re-fires. Never force-push over reviewed commits.

## Watchdog discipline (phantom-watchdog rule, 2026-09-06)

If you hand off to a detached worker and arm a cron watchdog over it:

1. NEVER register a watchdog against a script you have not verified exists on disk.
2. Use the REUSABLE monitor: do not write bespoke per-PR scripts:
   - Pre-PR: `~/.hermes/scripts/mar-monitor.sh --issue <N> --branch <N>-<slug> --wt <worktree-path>`
   - Post-PR: `~/.hermes/scripts/mar-monitor.sh --pr <PR>`
   Full arming checklist and state-line contract: `~/.hermes/scripts/mar-monitor-README.md`
3. Probe-run the exact monitor command; confirm one valid state line before reporting "watchdog armed". Then verify the first tick fired.
4. Commit checkpoints to the ticket branch at least every 30 minutes of active work: uncommitted work in a worktree is one hung worker away from being orphaned (645 lines sat uncommitted for 2h on #104 before anyone noticed).
5. Delete the watchdog cron when the PR merges or issue closes.

## Scope discipline

- Stay inside the ticket boundaries: "blocked by" tickets are not started early; follow-up enhancements stay out unless the ticket says otherwise.
- If a fix belongs to a different ticket, note it in the PR body as out-of-scope rather than implementing it.
