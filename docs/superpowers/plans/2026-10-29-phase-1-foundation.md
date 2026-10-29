# Phase 1: Foundation, Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Stand up the `websites-demo` repo so that Phase 2 (the first site) can start: a protected public repo, the approved spec committed, the shared rules files (`CLAUDE.md`, `DESIGN.md`), a README stub, and the docs MCPs connected.

**Architecture:**
- Generalize the owner's `$PROFILE` repo-creation function (`New-DotnetRepo` becomes `New-Repo`) and create the repo with it, making `C:\random-project\websites-demo` itself the repo root.
- Land the content as two small PRs into protected `main`, plus one PR for the project-scoped MCP config.
- No site code exists yet. `sites/` stays empty until Phase 2.

**Tech Stack:** PowerShell 7 (`$PROFILE`), GitHub CLI (`gh`), git, Claude Code CLI (`claude mcp`), Markdown.

**Spec:** `docs/superpowers/specs/2026-10-28-landing-page-portfolio-design.md` (approved 2026-10-29). Read sections 4, 5 and 6 before starting.

**Execution style (from spec section 7):** Claude carries out each step and **explains what it does and why** before moving on. The owner reviews as it goes. Pause after each task for the owner.

## Global Constraints

- Repo name: `websites-demo`. Public. MIT license.
- Repo root on disk: `C:\random-project\websites-demo`.
- `main` is protected by a ruleset (PRs required, no force-push, no deletion), and the only merge method is merge commits. **Never commit to `main`.** Every change goes on a branch, then a PR, then `gh pr merge --merge`.
- Commit messages follow Conventional Commits and end with the trailer `Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>`. PR bodies end with `🤖 Generated with [Claude Code](https://claude.com/claude-code)`.
- **Nothing personal from conversations goes into committed files.** That means no anecdotes, background stories or private motivations. Owner descriptor, verbatim: "backend .NET engineer, exploring web design and UI/UX".
- `.gitignore` is GitHub's **Node** template, plus `.vercel`.
- Docs-first rule: for any "how do we do X" question, check official docs first (Astro Docs MCP / Context7 for Tailwind / Vercel MCP or vercel.com/docs).

## Review Focus

1. **Spec lost during the move-out/clone/move-back dance.** Expect the spec and plan to survive byte-identical. *Pinned in Task 2, Step 1 (hash before) and Step 5 (hash after). The temp copy is deleted only after the hashes match.*
2. **Hidden files left in the folder make `gh repo clone` fail with "destination path already exists and is not an empty directory".** *Pinned in Task 2, Step 2: assert the folder is empty, including hidden files, before cloning.*
3. **A typo in `$PROFILE` breaks every new terminal.** Expect a fresh `pwsh` to start with no errors. *Pinned in Task 1, Step 5 (fresh-process load test). A backup is made in Step 1.*
4. **.NET repo creation silently changes.** Expect `New-DotnetRepo -Name x` to still produce a VisualStudio `.gitignore`. *Pinned in Task 1, Step 5: assert the alias exists and the default value is `VisualStudio`.*
5. **Personal or private text leaks into the public repo.** *Pinned in Task 3, Step 3 and Task 4, Step 6: grep the staged files for leak markers before committing.*

---

### Task 1: Generalize `New-DotnetRepo` → `New-Repo`

**Files:**
- Modify: `C:\Users\irfan\OneDrive\Documents\PowerShell\Microsoft.PowerShell_profile.ps1` (the `function New-DotnetRepo` block)
- Modify: `C:\Users\irfan\.claude\CLAUDE.md` (section "New project / new repo creation", item 6)
- Modify: `C:\Users\irfan\.claude\skills\git-guardrails\SKILL.md` (the line that names `New-DotnetRepo`)
- Backup: `<scratchpad>\Microsoft.PowerShell_profile.ps1.bak`

**Interfaces:**
- Produces: `New-Repo -Name <string> [-Description <string>] [-Path <string> = "C:\backend-dev"] [-Visibility public|private = "public"] [-Gitignore <string> = "VisualStudio"]`. Behavior is identical to the old function except that `--gitignore` takes `$Gitignore`. `Set-Alias New-DotnetRepo New-Repo` sits right after the function.

- [ ] **Step 1: Back up the profile.** Copy it to the scratchpad. Expected: the backup exists and has the same size as the original.
- [ ] **Step 2: Write the verification script (failing test).** Save it as `<scratchpad>\test-new-repo.ps1`. It runs in a fresh process: `pwsh -NoLogo -Command { . $PROFILE; ... }`. It asserts:
  - `(Get-Command New-Repo).CommandType -eq 'Function'`
  - `(Get-Command New-Repo).Parameters.ContainsKey('Gitignore')`
  - `(Get-Command New-DotnetRepo).CommandType -eq 'Alias'`, and the alias's `.Definition -eq 'New-Repo'`
  - the profile text matches `\[string\]\$Gitignore\s*=\s*"VisualStudio"`
  - the profile still contains `"--gitignore", $Gitignore`, and no longer contains `"--gitignore", "VisualStudio"`

  It prints `PASS`, or the first failing assertion.
- [ ] **Step 3: Run it to see it fail.** Expected: FAIL on `Get-Command New-Repo` (not found).
- [ ] **Step 4: Edit the profile.**
  - Rename the function.
  - Add the `[string]$Gitignore = "VisualStudio"` parameter.
  - Replace the hard-coded `"VisualStudio"` in `$ghArgs` with `$Gitignore`.
  - Update the `.SYNOPSIS`/`.EXAMPLE` comment so it covers both a .NET and a `-Gitignore Node` example.
  - Add `Set-Alias New-DotnetRepo New-Repo` after the function.

  Change nothing else in the function.
- [ ] **Step 5: Run the verification script.** Expected: `PASS`. Also confirm that `pwsh -NoLogo -Command "exit 0"` prints no errors, so the profile loads cleanly.
- [ ] **Step 6: Update the references.**
  - In global `CLAUDE.md` item 6, name `New-Repo -Name <repo-name> -Description "<short description>" [-Gitignore <template>]` as the function. Note that `New-DotnetRepo` remains as an alias, and that `-Gitignore` defaults to `VisualStudio`.
  - In `git-guardrails/SKILL.md`, replace `New-DotnetRepo` with `New-Repo`.

  Verify: `grep -n "New-DotnetRepo"` on both files returns only the alias mention in `CLAUDE.md`.
- [ ] **Step 7: No commit.** These files live outside any repo. Report the diff to the owner instead.

---

### Task 2: Create the `websites-demo` repo in place

**Files:**
- Temp move: `C:\random-project\websites-demo\docs\` → `C:\random-project\websites-demo-docs-tmp\docs\`
- Created by the tool: `.gitignore` (Node), `LICENSE` (MIT), `.git/`

**Interfaces:**
- Consumes: `New-Repo` from Task 1.
- Produces: a local clone at `C:\random-project\websites-demo` with `origin` set to `<owner>/websites-demo`, `main` checked out, and the repo's `docs/` restored but untracked.

- [ ] **Step 1: Hash the docs.** Record `Get-FileHash` (SHA256) for every file under `docs/`.
- [ ] **Step 2: Move `docs/` out, then assert the folder is empty.** Move `docs/` to `C:\random-project\websites-demo-docs-tmp\`. Then check that `Get-ChildItem -Force C:\random-project\websites-demo` returns nothing. Expected: 0 items, hidden files included. If anything else is there, stop and show the owner.
- [ ] **Step 3: Create the repo.** Run `New-Repo -Name websites-demo -Description "Concept landing pages and websites built from a clear spec and design rules" -Path C:\random-project -Gitignore Node`. Expected output includes:
  - `Ruleset applied`
  - `PR merge strategy restricted to 'merge commit' only.`
  - `Repo created and cloned at C:\random-project\websites-demo`
- [ ] **Step 4: Verify on GitHub.**
  - `gh repo view websites-demo --json visibility,licenseInfo,mergeCommitAllowed,squashMergeAllowed,rebaseMergeAllowed` should show `PUBLIC`, `mit`, `true`, `false`, `false`.
  - `gh api repos/{owner}/websites-demo/rulesets --jq '.[].name'` should show `main-protection`.
  - Local `.gitignore` should contain `node_modules/` and `.astro/`.
- [ ] **Step 5: Move `docs/` back and re-hash.** Every SHA256 should match Step 1. Delete `websites-demo-docs-tmp` **only after** they match.

---

### Task 3: PR #1: commit the spec, the plan and the `.gitignore` additions

**Files:**
- Add: `docs/superpowers/specs/2026-10-28-landing-page-portfolio-design.md`
- Add: `docs/superpowers/plans/2026-10-29-phase-1-foundation.md`
- Modify: `.gitignore` (append a `# Vercel` section containing `.vercel`)

**Interfaces:**
- Consumes: the clone from Task 2.

- [ ] **Step 1: Branch.** Run `git checkout -b docs/project-spec`.
- [ ] **Step 2: Append to `.gitignore` and verify.** Append the `.vercel` section. Then `git check-ignore -v .vercel/project.json node_modules/x dist/x .astro/x` should print a matching rule for all four paths.
- [ ] **Step 3: Leak check.** Run `git add -A`, then grep the staged diff against the private keyword list kept **outside the repo**, at `.superpowers/sdd/<plan>/leak-patterns.txt` (git-ignored). The list itself would hint at what it guards, so it is never committed. Run `git diff --cached | grep -inE -f <that file>`. Expected: no matches, apart from the allowed phrase "shadcn/MUI/Bootstrap look".
- [ ] **Step 4: Commit.** Message: `docs: add project spec and phase 1 plan`, with the trailer.
- [ ] **Step 5: Push, open the PR and merge.**
  - Run `git push -u origin docs/project-spec`.
  - Run `gh pr create` with a summary of the spec and the PR footer.
  - The owner reviews, then `gh pr merge --merge --delete-branch`.

  Expected: the PR shows as merged, and `git checkout main && git pull` brings in both docs.

---

### Task 4: PR #2: `CLAUDE.md`, root `DESIGN.md`, README stub, `sites/`

**Files:**
- Create: `CLAUDE.md`, `DESIGN.md`, `README.md`, `sites/.gitkeep`

**Interfaces:**
- Produces: the rules files that every Phase 2+ task reads. `CLAUDE.md` links to `DESIGN.md` and to the spec. It does not repeat them.

- [ ] **Step 1: Branch.** Run `git checkout -b chore/project-conventions`.
- [ ] **Step 2: Write `DESIGN.md`.** Copy spec section 4's "Root DESIGN.md" items (a)–(f) as the rules, with the 10 mobile rules **verbatim**.
  - Expand (b) with the full CTA behavior from "Content and honesty": first tap opens a modal (`<dialog>`), later taps show a toast (`aria-live="polite"`, about 5s), state kept in `sessionStorage`, anchor links never intercepted, disclosure in the footer only, no top banner.
  - Expand (e) with the three approved sources and the "no AI-generated photos" line.
  - Open with the precedence rule: *"A site's own DESIGN.md may add to these rules but never contradict them."*
- [ ] **Step 3: Write `CLAUDE.md`.** Use these sections, each a short list:
  1. **Project:** a one-paragraph purpose (spec section 1, both purposes) plus a link to the spec.
  2. **Stack:** Astro + Tailwind, no UI component library, npm, each `sites/<name>/` an independent Astro project.
  3. **Docs first:** the rule from spec section 4, verbatim, with all three sources.
  4. **Official MCPs:** the standing rule.
  5. **Per-site workflow:** `BRIEF.md` (including "Site scope"), then `DESIGN.md` (each choice traced to the brief, checked against earlier sites with the "same template?" question), then build section by section with screenshots at 375/768/1280, then the polish skills, then the AI-tells check, then the owner's review.
  6. **Design rules:** "All sites must satisfy root `DESIGN.md`." (a link, not a copy).
  7. **Git:** the Global Constraints git lines, plus **"Before any push: `npm run build` must pass in every changed `sites/*` project."**
  8. **Way of working:** discuss decisions until they're understood, explain each step, and keep conversational/personal context out of committed files.
- [ ] **Step 4: Write `README.md`.**
  - Title and the two purposes (public-safe wording from spec section 1).
  - A "**Concept projects:** all businesses shown are fictional" note.
  - A "Sites" table with columns `Site | Industry | Live link`, empty except for one line: "First site in progress."
  - A "How these are made" paragraph: a written spec, shared design rules, a brief and design notes per site, AI-assisted with Claude Code. Link to `docs/`.
- [ ] **Step 5: Add `sites/.gitkeep`** (empty file).
- [ ] **Step 6: Run the leak check** from Task 3, Step 3 on the staged files. Expected: no matches.
- [ ] **Step 7: Commit, push, open the PR, merge.** Message: `docs: add CLAUDE.md, DESIGN.md and README`. Same PR flow as Task 3, Step 5. The owner reads all three files in the PR before merging.

---

### Task 5: PR #3: connect the docs MCPs

**Files:**
- Create: `.mcp.json`, written by `claude mcp add -s project`. It's committed so that anyone cloning the repo gets the same docs server.

**Interfaces:**
- Produces: the tools `mcp__astro-docs__search_astro_docs`, Context7's docs tools and the Vercel connector tools, all available to Phase 2.

- [ ] **Step 1: Branch.** Run `git checkout -b chore/mcp-config`.
- [ ] **Step 2: Add the Astro Docs MCP at project scope.** Run `claude mcp add --transport http -s project astro-docs https://mcp.docs.astro.build/mcp`. Expected: `.mcp.json` exists and contains `"astro-docs"` with that URL.
- [ ] **Step 3: Owner sign-ins** (interactive, done by the owner).
  - Authenticate the Context7 plugin.
  - Authenticate the claude.ai Vercel connector.

  Restart or reconnect MCP servers (`/mcp`) so the new tools load.
- [ ] **Step 4: Verify each source with one real query.**
  - **Astro:** `search_astro_docs("Image component responsive images")` returns Astro docs content that mentions `<Image />` or `<Picture />`.
  - **Context7:** resolve `tailwindcss`, then fetch docs for `@theme`. Expected: returns Tailwind v4 content.
  - **Vercel:** list the owner's teams or projects. Expected: a response, not an auth error.
- [ ] **Step 5: Commit, push, open the PR, merge.** Message: `chore: add project-scoped Astro Docs MCP config`. Same PR flow as Task 3.

---

**Phase 1 done when:**
- the repo is public and protected on GitHub;
- `main` contains the spec, the plan, `CLAUDE.md`, `DESIGN.md`, `README.md`, `sites/.gitkeep`, `.mcp.json` and the extended `.gitignore`;
- all three docs sources answer a query.

Phase 2 (first site) gets its own plan.
