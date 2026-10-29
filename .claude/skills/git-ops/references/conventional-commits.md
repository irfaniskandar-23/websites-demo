# Conventional Commits 1.0.0: reference

A summary of [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/), written for this repo. The original specification is licensed under [CC BY 3.0](https://creativecommons.org/licenses/by/3.0/). Read this file instead of fetching the website.

## Structure

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

## The rules

1. Every commit **must** start with a type, then an optional scope in parentheses, an optional `!`, then a colon and a space.
2. `feat` **must** be used for a new feature. `fix` **must** be used for a bug fix.
3. Other types are allowed. The spec mentions `build`, `chore`, `ci`, `docs`, `style`, `refactor`, `perf` and `test`, plus `revert` for reverts.
4. A scope is a noun naming part of the codebase, in parentheses: `fix(solidvent):`.
5. The description comes straight after `: ` and is a short summary of the change.
6. A body is optional. It starts **one blank line** after the description and can have several paragraphs.
7. Footers are optional. They start one blank line after the body. Each footer is a token, then `: ` (or ` #`), then a value. Tokens use `-` instead of spaces, e.g. `Co-Authored-By: ...`, `Refs: #12`.
8. **Breaking changes** are marked either with `!` before the colon (`feat(solidvent)!: ...`) or with a footer `BREAKING CHANGE: <what broke>`. `BREAKING CHANGE` must be uppercase, and `BREAKING-CHANGE` means the same thing.
9. Apart from `BREAKING CHANGE`, nothing is case-sensitive.

Versioning link (for reference only; these sites aren't versioned): `fix` = patch, `feat` = minor, a breaking change = major.

## Which type to use in this repo

| Type | Use it for | Example |
|---|---|---|
| `docs` | `BRIEF.md`, `DESIGN.md`, build plans, README, `CLAUDE.md`, skills | `docs(solidvent): add business brief` |
| `build` | Scaffolding, dependencies, Astro, Tailwind or Vercel config | `build(solidvent): scaffold astro project` |
| `feat` | A new section or visible behavior on a site | `feat(solidvent): add pricing table` |
| `fix` | Something broken: layout bug, contrast failure, wrong copy | `fix(solidvent): stop horizontal scroll at 320px` |
| `perf` | Speed: image sizes, font loading, Lighthouse work | `perf(solidvent): serve smaller hero image on phones` |
| `refactor` | Restructuring code with no visible change | `refactor(solidvent): split footer into its own component` |
| `style` | **Code formatting only** (whitespace, quotes). Not visual design. A design change is `feat` or `fix`. | `style(solidvent): format components with prettier` |
| `chore` | Housekeeping that fits nowhere else, e.g. `.gitignore` | `chore: ignore vercel output folder` |
| `ci` | GitHub Actions or other CI | `ci: add lighthouse check on preview deploys` |
| `test` | Tests | `test(solidvent): check cta modal opens once per visit` |
| `revert` | Undoing an earlier commit, with a `Refs:` footer naming it | `revert: undo pricing table layout` |

Scope is the site folder name (`solidvent`). Repo-wide commits have no scope.

## A full example from this repo

```
feat(solidvent): add sample service report section

Shows the before/after readings card that replaces testimonials,
as decided in the brief.

Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>
```

The `Co-Authored-By` trailer is a regular Conventional Commits footer (rule 7).
