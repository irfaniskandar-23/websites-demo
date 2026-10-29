# Shared design rules

Every site in `sites/` must meet these rules. **A site's own `DESIGN.md` may add to these rules but never contradict them.** Each site picks its own look (palette, typography, layout) in its own `DESIGN.md`. This file sets the floor that all of them stand on.

## 1. Mobile

Each rule is pass/fail.

1. **Design phone-first.** Build the phone layout first, then widen it.
2. **Check at three widths:** 375px, 768px and 1280px.
3. **No horizontal scrolling** at any width down to 320px.
4. **Tap targets are at least 44×44px**, with spacing between them.
5. **Nothing depends on hover.**
6. **Body text is at least 16px.**
7. **The primary CTA is visible without scrolling on a 375×667 screen**, either in the first screen or as a sticky bottom button.
8. **Images are sized per screen**, so phones download smaller versions.
9. **Lighthouse mobile scores are ≥ 90** for Performance and Accessibility.
10. **Verify** with screenshots at all three widths before review. The owner then checks the Vercel preview on a real phone before merging.

## 2. Call-to-action behavior

Every business on these sites is fictional, so primary CTAs ("Book now", "WhatsApp us", "Get a quote", "Subscribe") must respond when tapped without pretending to work.

- **First CTA tap in a visit:** open a small modal (native `<dialog>`). It says:
  - that this is a concept site;
  - what the button would do for a real business;
  - "Want a site like this?", with links to the owner's email and LinkedIn.
- **Later CTA taps in the same visit:** show a short toast (`aria-live="polite"`, visible for about 5 seconds), e.g. *"This is a concept site, so booking isn't live."*
- **A "visit"** is one browser tab session. Remember the dismissal in `sessionStorage`.
- **Ordinary navigation is never intercepted.** That includes menu links and anchor links to page sections.
- **Never use fake phone numbers, fake emails or working forms.**
- **No top banner.** Persistent disclosure lives in the footer only (see §4).
- **Content and behavior are identical on every site.** Styling follows each site's own design: playful on a café site, formal on a government-style one.

## 3. Accessibility basics

- Text contrast meets WCAG AA.
- Keyboard focus is always visible.
- Motion respects `prefers-reduced-motion`.
- Every meaningful image has alt text. Decorative images use `alt=""`.

## 4. Concept-project footer

Every site's footer says the business is fictional and links to the owner's email and LinkedIn, e.g. *"This is a concept project for a fictional business. Designed by Irfan. Get in touch by email or on LinkedIn."*

## 5. Images

Use online image resources that fit the site's context. Approved sources:

| Source | Use for |
|---|---|
| [unDraw](https://undraw.co) | Illustrations |
| [Unsplash](https://unsplash.com) | Photos |
| [Pexels](https://pexels.com) | Photos |

More sources may be added here later. **No AI-generated photos.**

## 6. Copy

- Sentence case for headings, buttons and labels.
- CTAs say exactly what they do ("Book a table", not "Submit").
- No lorem ipsum and no Faker-generated text. Every word is written for that business.
