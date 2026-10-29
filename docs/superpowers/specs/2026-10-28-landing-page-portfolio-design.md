# Landing Page Portfolio — Design

**Date:** 2026-10-28
**Status:** Approved by owner on 2026-10-29
**Owner:** Irfan (backend .NET engineer, exploring web design and UI/UX)

---

## 1. Intent and why

A public GitHub repo containing a collection of landing pages and small static websites. Each one turns a business idea into a polished, professional site.

The repo has two purposes:

1. **Show the work.** Recruiters and potential freelance clients should be able to see that the owner builds quality, professional sites.
2. **Show the method.** Produce quality, professional demo sites that avoid the AI-generated look by working from a **clear spec and design philosophy**: a brief per site, shared design rules, and documented decisions. This is the opposite of repeatedly re-prompting an AI site builder until something looks acceptable.

## 2. Audiences

| Audience | What they look at | What they need to conclude |
|---|---|---|
| Recruiters | The GitHub repo: README, code structure, commit history | "This person builds quality sites and works in a professional way" |
| Potential freelance clients | Live links, mostly on a phone, for about 10 seconds | "I'd trust this person with my business's website" |

No freelance niche has been chosen yet. Covering several industries is intentional: it shows range and may reveal a niche later.

## 3. Goals, non-goals, anti-goals

**Goals**
- Several distinct, professional landing pages and websites, each live on its own URL.
- Visitors should not be able to tell **at a glance** that a site was AI-generated.
- The owner learns design: every design decision has a stated reason the owner understands and can defend in an interview.
- The repo itself shows a professional workflow: PRs, Conventional Commits, documented decisions.

**Non-goals**
- Web applications. That means no auth, no databases, no dashboards, no backend logic. The scope is strictly websites and landing pages.
- Hiding that AI assisted the work. AI-assisted development is openly fine. The CLAUDE.md file, skills and co-author lines stay visible in the repo.
- A shared component library or design system across sites (not yet).

**Anti-goals** (the project fails even if everything technically works when…)
- Sites look AI-generated or templated: generic SaaS card grids, default font and color pairings, the shadcn/MUI/Bootstrap look, AI-generated photos.
- Sites look alike, as if one template made them all. Note that reusing a font or color is fine. The failure is when the **overall look** is the same.
- The owner ships sites they can't explain.
- A fictional site could be mistaken for a real business or a real government body.

## 4. Decisions made (by the owner, during brainstorming)

**Tools**
- **Astro** as the framework (chosen over Next.js because these are content sites and don't need app infrastructure).
- **Tailwind CSS** for styling.
- **No UI component library.** Components are custom-built with Tailwind so each site can have its own look.
- **Hand-written copy** in content files. No faker or lorem ipsum.

**Repo structure:** one repo (a monorepo), with each site as a fully independent Astro project. Nothing is shared at first; shared pieces get added only once repetition actually hurts.

```
websites-demo/
├── CLAUDE.md              # HOW we work: tools, git, process (for Claude)
├── DESIGN.md              # design rules EVERY site must meet (mobile support, CTA behavior, ...)
├── README.md              # showcase: screenshot + live link per site, "concept project" note
├── docs/                  # plans and specs
├── .claude/skills/        # project skills (site-brief comes later; see phases)
└── sites/
    └── <site-name>/
        ├── BRIEF.md       # the business: problem, audience, edge, pricing, personality
        ├── DESIGN.md      # how it looks and WHY, with each choice traced to BRIEF.md
        └── (Astro project)
```

- **BRIEF.md vs DESIGN.md.** The test for where something goes: *"Would this still be true if the website didn't exist?"* If yes, it goes in BRIEF. If no, it goes in DESIGN. The brief is written first, and the design is worked out from it.
- **Root DESIGN.md (owner's decision).** Shared design rules that every site must meet. A site's own DESIGN.md may add to them but never contradict them. CLAUDE.md points to the root DESIGN.md instead of repeating it. Confirmed contents:
  - (a) **mobile rules** (each one can be checked as pass or fail):
    1. Design phone-first: build the phone layout first, then widen it.
    2. Check at 375px, 768px and 1280px.
    3. No horizontal scrolling at any width down to 320px.
    4. Tap targets are at least 44×44px, with spacing between them.
    5. Nothing depends on hover.
    6. Body text is at least 16px.
    7. On a 375×667 screen, the primary CTA is visible without scrolling, either in the first screen or as a sticky bottom button.
    8. Images are sized per screen; phones download smaller versions.
    9. Lighthouse mobile scores are ≥ 90 for Performance and Accessibility.
    10. Verify with screenshots at all three widths before review, then the owner checks the Vercel preview on a real phone before merging.
  - (b) the CTA behavior described under "Content and honesty";
  - (c) accessibility basics: readable contrast, visible keyboard focus, respecting reduced-motion settings, alt text on images;
  - (d) the concept-project footer, with the owner's email and LinkedIn;
  - (e) the image rule and approved sources described under "Images";
  - (f) copy rules: sentence case, CTAs that say exactly what they do, no lorem ipsum or Faker.
- **Site scope is decided per site (owner's decision).** Each BRIEF.md includes a short "Site scope" section saying whether the site is a single landing page or a small multi-page website, based on what that business needs. There is no project-wide default. Multi-page sites take roughly 3–5× the work, which affects pace.
- No separate design log. Before designing a new site, read the earlier sites' DESIGN.md files and ask: *"Would someone looking at both sites think the same template made them?"*

**Deployment**
- **Vercel**, with one Vercel project per site folder (its Root Directory set to `sites/<site-name>`). Each site gets its own link. Every push to a site's branch gets a preview URL, so the site can be checked on a phone before its PR is merged.
- A main portfolio link (one page linking to every site) is **postponed** until 2 or more sites exist.

**Docs first, before building (owner's rule, goes into the project CLAUDE.md).** For any "how do we do X" question, check the relevant tool's official docs **before** proposing or writing a custom solution:
- **Astro** through the Astro Docs MCP
- **Tailwind** through Context7, which reads Tailwind's official docs
- **Vercel** through the Vercel MCP or vercel.com/docs

Answer two questions: does the tool already provide this, so we don't reinvent the wheel; and does it offer a better approach than the one we have in mind? Present the docs' answer, with a citation, before any custom approach.

**AI tooling.** This is a standing rule: for every tool chosen, check for an **official** MCP server.
- Astro Docs MCP (official): `claude mcp add --transport http astro-docs https://mcp.docs.astro.build/mcp`
- Tailwind: no official MCP exists. Use the Context7 plugin for current docs.
- Vercel MCP (official, beta, mostly read-only): use the existing claude.ai Vercel connector. It needs a one-time sign-in.
- The design work runs through the `frontend-design` skill: write a token plan, check it against the brief, build, then critique with screenshots taken through claude-in-chrome.
- The `design:design-critique`, `design:accessibility-review` and `design:ux-copy` skills are used for a polish pass.

**Content and honesty**
- Each fictional business is based on a **real business category** (for example, a real kind of Malaysian café or clinic), with a fictional name and details.
- No real brands, real government bodies, crests or logos. Government-style sites use fictional agencies.
- Every site footer carries a line like *"Concept project — fictional business"*, and the README says the same.
- **Calls to action (owner's decision, refined after a design critique).** Every primary CTA (such as "Book now", "WhatsApp us", "Get a quote" or "Subscribe") responds when tapped. There are no fake phone numbers or emails, no working forms, and no top banner.
  - **First CTA tap in a visit:** a small modal (native `<dialog>`) says this is a concept site, says what the button would do for a real business, and offers "Want a site like this?" with the owner's **email and LinkedIn**.
  - **Later CTA taps in the same visit:** a short toast (`aria-live="polite"`, visible for about 5 seconds), e.g. *"Concept site — booking isn't live."*
  - "Visit" means one browser tab session (`sessionStorage`).
  - Ordinary navigation links (such as anchor links to sections) are never affected.
  - Persistent disclosure lives in the footer only.
  - Content and behavior are identical across sites. Styling follows each site's design.
  - The owner's contact email and LinkedIn URL are needed at build time.

**Images (owner's wording).** Use online image resources that fit the site's context. Approved sources:
  - **unDraw** (undraw.co): illustrations
  - **Unsplash** (unsplash.com): photos
  - **Pexels** (pexels.com): photos

  More sources can be added later after research. No AI-generated photos (anti-goal).

**Way of working**
- Discuss and iterate on decisions until the owner understands them, before any groundwork.
- Build code **one step at a time** so the owner can see how the pieces fit together.
- Git: feature branches, PRs, Conventional Commits and merge commits only, per the global CLAUDE.md.
- **Build before push (owner's decision).** `npm run build` must pass for every changed `sites/*` project before any push. The project's [`git-ops`](../../../.claude/skills/git-ops/SKILL.md) skill enforces this, along with the rest of the git workflow.
- Once Vercel is connected, its preview build on each PR acts as a second, server-side check. GitHub Actions is not needed for now.
- Done check for each site: the owner and Claude review the result together. Before that review, Claude checks the site against the `frontend-design` list of AI-generated tells.

**`/site-brief` skill:** write the first 1–2 briefs by hand. After that, turn what was learned into a project skill at `.claude/skills/site-brief/SKILL.md` that asks only what it can't guess, drafts the rest, and marks its guesses as guesses.

## 5. Phases

0. **Plan:** this document, approved.
1. **Foundation (once):**
   - Create the repo following section 6: generalize `New-Repo`, then create the repo with a Node `.gitignore`, a ruleset on `main` and merge commits only.
   - Set up the folder skeleton, root `CLAUDE.md`, root `DESIGN.md` and a README stub.
   - Connect the MCPs (Astro Docs, Context7, Vercel sign-in).
2. **First site** (all on one branch, `feat/<site>`, from brainstorming until it's live):
   - Write `BRIEF.md` by hand, together.
   - Write `DESIGN.md` (token plan, checked against the brief).
   - Scaffold the Astro project and create its Vercel project, so every later push to the branch gets a preview link.
   - Build it section by section, with screenshots at desktop and phone widths.
   - Run the polish pass.
   - Open the site's PR, only once the site is complete. The owner reviews the Vercel preview. Add the live link to the README, then merge the PR to `main` (this deploys production).
3. **Phase 2.5:** after 1–2 sites, create the `/site-brief` skill.
4. **Repeat for each site.** Add the main portfolio page once 2–3 sites exist.

The owner's pace target is 2–3 sites per day. Expect the first site to be slower because it is also the learning pass.

## 6. Repo setup decisions

- **Repo name:** `websites-demo` (public, MIT).
- **Local folder:** `C:\random-project\websites-demo` is the repo root itself.
- **`main` is protected (PRs required)**, consistent with the owner's other repos. Every PR doubles as a Vercel preview (a staging check), and the PR history is portfolio evidence.

**Steps:**
1. **Generalize the `$PROFILE` function.**
   - Rename `New-DotnetRepo` → `New-Repo`.
   - Add a `-Gitignore` parameter that defaults to `VisualStudio`, so .NET behavior is unchanged.
   - Keep `New-DotnetRepo` as an alias so nothing breaks.
   - Update the global `~/.claude/CLAUDE.md` and the git-guardrails skill's mention of the function to match.
2. **Move `docs/` out temporarily.** This folder already holds this spec, and `gh repo clone` refuses to clone into a non-empty folder.
3. **Run `New-Repo -Name websites-demo -Path C:\random-project -Gitignore Node`.**
4. **Move `docs/` back and commit it on a branch** as the first PR.

## 7. Claude's calls (owner can override)

- **Who writes code:** Claude writes it one step at a time and explains each step, and the owner reviews as it goes. Every site follows the full process; later sites move faster because the steps are familiar.
- **Free `*.vercel.app` addresses** for each site. There's no custom domain for now, and the "concept project" footer covers the risk of a site being mistaken for a real business.
- **npm** as the package manager. Sites are independent, so no workspace tooling is needed.
- **English** copy by default. Malay or local flavor is used where the brief calls for it.
- **Current Astro and Tailwind setup.** Add Tailwind using Astro's officially documented integration path. Fonts are self-hosted using whatever approach the current Astro docs recommend, verified through the Astro Docs MCP rather than from memory.

## 8. Left to the builder

- The exact contents of `CLAUDE.md` and the README layout, within the rules above.
- Each site's visual direction, worked out per site from its BRIEF.md during Phase 2.
