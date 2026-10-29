# websites-demo

## Project

A collection of concept landing pages and small static websites, each one turning a fictional business (based on a real business category) into a polished, professional site. The repo has two purposes:

1. **Show the work:** recruiters and potential freelance clients can see quality, professional sites.
2. **Show the method:** sites that avoid the AI-generated look because they're built from a clear spec and design philosophy, not by repeatedly re-prompting an AI builder.

Full spec: [`docs/superpowers/specs/2026-10-28-landing-page-portfolio-design.md`](docs/superpowers/specs/2026-10-28-landing-page-portfolio-design.md). Scope is websites and landing pages only: no auth, databases, dashboards or backend logic.

## Stack

- **Astro** for structure, **Tailwind CSS** for styling.
- **No UI component library** (no shadcn, MUI, Ant, Bootstrap). Components are hand-built so each site can have its own look.
- **Fonts** are self-hosted, using whatever approach the current Astro docs recommend (check the Astro Docs MCP rather than memory).
- **npm.** Each `sites/<name>/` is an independent Astro project with its own `package.json`. There is no shared code or workspace tooling until repetition actually hurts.
- **Vercel:** one Vercel project per site, with Root Directory = `sites/<name>`, on a free `*.vercel.app` address.

## Docs first

For any "how do we do X" question, check the relevant tool's official docs **before** proposing or writing a custom solution:

- **Astro:** the Astro Docs MCP (`astro-docs`)
- **Tailwind:** Context7, which reads Tailwind's official docs
- **Vercel:** the Vercel MCP or vercel.com/docs

Answer two questions: does the tool already provide this (don't reinvent the wheel), and does it offer a better approach than the one in mind? Present the docs' answer, with a citation, before any custom approach.

## Official MCPs

For every tool added to the stack, check whether it publishes an **official** MCP server and recommend connecting it. Verify this against the vendor's own docs and tell official servers apart from community ones.

## Per-site workflow

1. **`sites/<name>/BRIEF.md`:** the business: problem, audience, edge over competitors, pricing, personality, and a **Site scope** section (single landing page or small multi-page site). Test for what belongs here: *"Would this still be true if the website didn't exist?"*
2. **`sites/<name>/DESIGN.md`:** the site's look, with every choice traced back to the brief ("X, because the brief says Y"). Use the `frontend-design` skill: make a token plan, then check it against the brief. Before finalizing, read the earlier sites' `DESIGN.md` files and ask: *"Would someone looking at both sites think the same template made them?"*
3. **Scaffold and link:** create the Astro project in `sites/<name>/`. In the same PR, create its Vercel project (Root Directory = `sites/<name>`) so every later PR for the site gets a preview link.
4. **Build section by section,** taking screenshots at 375, 768 and 1280px after each section.
5. **Polish pass:** `design:design-critique`, `design:accessibility-review`, `design:ux-copy`.
6. **AI-tells check:** compare the site against the `frontend-design` list of common AI-generated patterns.
7. **Owner review** on the Vercel preview, then merge to `main` (this deploys production) and add the live link to the README.

## Design rules

All sites must satisfy the root [`DESIGN.md`](DESIGN.md). A site's own `DESIGN.md` may add to it but never contradict it.

## Git

- Never commit or push to `main`. Work on a branch, open a PR, then merge with `gh pr merge --merge`. Merge commits only, never squash or rebase.
- Conventional Commits for every message.
- **Before any push: `npm run build` must pass in every changed `sites/*` project.**
- Every PR gets a Vercel preview. Check it on a real phone before merging.

## Way of working

- Discuss decisions until the owner understands the reasoning, before doing any groundwork.
- Build one step at a time and explain each step.
- Keep anything personal the owner shares in conversation **out of committed files**: specs, READMEs, commit messages, PR descriptions. Ask when unsure.
