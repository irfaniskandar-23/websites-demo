---
name: git-ops
description: Use for every git operation in websites-demo (branching, committing, pushing, opening and merging PRs, syncing with main, resolving conflicts).
---

# Git operations for websites-demo

## 1. Working on a branch

Every time, in this order:

1. **Get on the branch and bring in the latest `main`, before touching any file.**
   - The branch already exists (e.g. coming back to a site, even one that's already live):
     ```
     git checkout <branch> && git pull --no-rebase origin main
     ```
   - New branch (a new site, or new repo-wide work):
     ```
     git checkout main && git pull --no-rebase origin main && git checkout -b <branch>
     ```
2. **Do the work** and commit it (§3).
3. **Run the pre-push checks** (§4), push, and open or update the PR (§5).

Never create or edit files on `main`.

`--no-rebase` keeps the always-merge rule even on a machine whose git config says `pull.rebase = true`.

If any command fails (offline, auth prompt, diverged `main`), stop and report the error. If the pull hits conflicts, follow §7. If the working tree has uncommitted changes, report them and ask the owner what to do. Do not stash them away.

## 2. Branches

**One branch per site, from brainstorming until it's live.** The site's brief, design notes, build plan, code and README link all go on that one branch, named `feat/<site>` (e.g. `feat/solidvent`).

- A site branch never changes another site. Repo-wide files (root `CLAUDE.md`, root `DESIGN.md`, `README.md`) may change on it only when the change exists because of that site.
- Repo-wide work that isn't caused by a site gets its own short branch, `<type>/<short-description>` (e.g. `chore/git-ops-skill`).
- **Branches are kept** after merging, locally and on GitHub. Later changes to a live site go on the same `feat/<site>` branch, starting from §1 step 1.

## 3. Committing

- Conventional Commits 1.0.0. The rules and the type to use for each kind of work in this repo are in [`references/conventional-commits.md`](references/conventional-commits.md). Read that file instead of fetching the website.
- The type says what kind of work each commit is, so one site branch carries many types: `docs(solidvent): add business brief`, `build(solidvent): scaffold astro project`, `feat(solidvent): add pricing section`. Scope = the site folder name. No scope for repo-wide work.
- Summary in the imperative mood, lowercase, no trailing period.
- Pass multi-line messages through a heredoc.
- End every message with the trailer `Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>`.
- Stage files by name. Never `git add -A` or `git add .`, so stray files don't slip in.

## 4. Before every push

Run these in order. If any one fails, stop and report. Do not push.

1. **Branch check:** not on `main`.
2. **Build check:** run `npm run build` in every `sites/<name>/` changed on this branch that has a `package.json`:
   ```
   git diff --name-only origin/main...HEAD -- sites/
   ```
   A site with only docs (no `package.json` yet) has nothing to build.
3. **Leak check:** if `.superpowers/sdd/leak-patterns.txt` exists, search the branch diff, the commit messages and the PR body for its patterns. Check the exit code: `grep` returns 0 for a match, 1 for no match, and 2 or more for an error. Only exit 1 is a pass. Any match means stop and ask the owner.

## 5. Pull requests

- **One PR for a site's build.** Open it as a draft (`gh pr create --draft`) on the site branch's first push, so the work is visible as it grows. Mark it ready (`gh pr ready`) when the site is ready for the owner's review. Later changes to a live site open a new PR from the same branch.
- Once the site's Vercel project is linked, every push to the site branch gets its own preview deployment. The owner checks the latest preview on a real phone before merging.
- The body says what changed and why, and ends with:
  `🤖 Generated with [Claude Code](https://claude.com/claude-code)`
- Nothing personal from the conversation goes into PR titles or bodies.

## 6. Merging

- Merge **only when the owner says so, for that PR**. Approval for one PR does not carry over to the next.
- `gh pr merge <number> --merge`. Merge commits only, never squash or rebase.
- Afterwards: `git checkout main && git pull --no-rebase origin main`. Keep the branch.

## 7. Conflicts

1. List every conflicted file.
2. For each one, explain both sides in plain words: what changed here, what changed on `main`, and why they collide.
3. Propose a resolution and **wait for the owner's approval** before writing it.
4. After resolving, run the build check (§4.2) again.

## Never

- Create, edit, commit or push on `main`.
- Rebase, force-push, or use `--no-verify`.
- Delete a branch.
- Merge without the owner's go-ahead for that PR.
