# NachoTek customization branch

This fork keeps Matt Pocock's upstream skills syncable while preserving NachoTek-specific behavior.

## Branches

- `main`: exact mirror of `mattpocock/skills` `main`. Do not add local commits here.
- `nachotek/customizations`: small patch stack rebased onto upstream `main`.

Each customized skill should have its own commit so a change can be dropped cleanly if upstream absorbs it or the policy moves into a project `AGENTS.md`.

## Remotes

```text
origin    https://github.com/NachoTek/skills.git
upstream  https://github.com/mattpocock/skills.git
```

## Sync from upstream

Run from a clean checkout with GitHub write access to `NachoTek/skills`:

```bash
git fetch --prune upstream
git fetch --prune origin

git switch main
git reset --hard upstream/main
git push --force-with-lease origin main

git switch nachotek/customizations
git rebase upstream/main
git diff --check upstream/main..HEAD
git push --force-with-lease origin nachotek/customizations
```

Resolve any rebase conflict in the customization commit that owns the affected skill. Afterward, verify marker content from both upstream and the local commit rather than trusting a clean merge alone.

## MeetAndRead deployment

The MeetAndRead VM checkout is `C:\Users\CCSupport\repos\skills` on `nachotek/customizations`.

OpenCode skill directories are NTFS junctions into that checkout. Switching or updating the checkout updates future skill loads without copying files. Do not edit the junction targets without committing the result to this branch.

Current customization markers:

- `skills/engineering/implement/SKILL.md`: upstream ticket-reference rule plus `PR publication`, `Watchdog discipline`, and `Scope discipline`.
- `skills/in-progress/chief-of-staff/SKILL.md`: `External gates and session continuity` plus `Closure ownership`.

Before declaring a sync complete, verify:

```bash
git status --short
git rev-list --left-right --count upstream/main...HEAD
git diff --check upstream/main..HEAD
```

The expected status is a clean worktree, zero commits behind upstream, and only the intentional customization commits ahead.
