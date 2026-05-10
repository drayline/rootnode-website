# CLAUDE.md — rootnode-website

## Mission

This repository is the source of truth for **rootnode.design**, the marketing and distribution surface for root.node. Its job is to convert practitioners who care about Claude Project architecture into Skill installers (open-core) and, eventually, commercial customers (when paid tier launches).

"Shipped" means: a change committed to `main`, auto-deployed via Cloudflare Pages, and verified live at https://rootnode.design with no broken links, no console errors, and tagline integrity preserved.

The site is a static, single-page HTML deployment. No build pipeline, no framework, no runtime dependencies beyond fonts loaded from Google Fonts. Treat the simplicity as load-bearing — every dependency added expands attack surface and deploy fragility.

---

## Authority matrix

Three tiers govern what content classes you may modify autonomously, modify-with-notification, or escalate.

| Tier | Behavior | Content class |
|---|---|---|
| **Tier 1 — Source-only / mirror-exact / escalate** | Cannot modify without explicit operator instruction. Surface any approach to a Tier 1 boundary. | Tagline copy in all locations (currently "The architecture system for Claude" / title-case "The Architecture System for Claude"); Schema.org JSON-LD structured data shape; brand assets in `/assets/` (logo SVG, layer diagram SVGs); canonical URL, og:url, sitemap.xml; meta description and social descriptions (search snippet impact); favicon set; OG image. |
| **Tier 2 — Mirror with notification** | May modify; log entry in `change_log.md`. | Section copy (Problem, Solution, Skills, Practice, Compilation, Use Case, Footer prose); hero headline and subheadline copy; hero stats line (Skill counts, Block counts); new section additions when explicitly tasked. |
| **Tier 3 — Free design** | Modify autonomously. | CSS variables and tokens (colors, spacing, typography); internal layout structure within sections; animation timing; accessibility improvements (ARIA, semantic HTML, alt text); performance optimizations (image compression, font loading); responsive breakpoints; new utility classes. |

The tagline is locked as Tier 1 because brand-line drift is a regression vector. Once the locked phrasing is set, you do not autonomously paraphrase, restructure, or "improve" it — even if a Tier 2 copy edit would make adjacent prose flow better. Surface the friction; do not absorb it.

---

## Bug-fixing authorization scope

**In-scope (proceed without surfacing):** Tier 2 copy edits within the current task scope. Tier 3 changes. Bug fixes (broken links, console errors, HTML validation failures, accessibility violations). Asset optimization (image compression, inline critical CSS).

**In-scope-with-notification (proceed; log in `change_log.md`):** Tier 2 copy changes that go beyond the immediate task scope (cleanup-as-you-go). Refactors that touch multiple sections. Sitemap/robots updates that reflect actual site structure changes.

**Out-of-scope (halt and escalate):** Tier 1 modifications without explicit task authorization. Architecture changes (single-file → multi-file split, build pipeline introduction, framework adoption). Domain or DNS changes. Cloudflare Pages configuration changes (handled in CF dashboard by operator). Adding analytics, tracking, or any new external runtime dependency. Any commit that would change `<head>` script tags or external resource references.

---

## Halt-and-escalate triggers

These are non-negotiable. When any trigger fires, stop, surface to operator with the specific trigger named, and wait for resolution. Do not iterate around a halt.

1. **Tier 1 boundary approach** — about to modify locked content (tagline, brand assets, JSON-LD shape, meta descriptions, canonical URLs) without explicit task authorization for that specific modification.
2. **Brand-asset modification** — about to touch any file in `/assets/`.
3. **External script or CDN addition** — security surface expansion warrants explicit approval. This includes new font sources, analytics scripts, embed widgets, third-party APIs.
4. **Routing or SEO change** — modifications to `sitemap.xml`, `robots.txt`, canonical URLs, redirects, or any header that affects search indexing.
5. **Build or deploy infrastructure introduction** — moving from static-HTML-as-source to a build pipeline (PostCSS, bundler, framework, etc.) is a major architectural decision.
6. **Visual regression risk** — if a CSS or HTML change might break layout on viewports you cannot verify (mobile, narrow, ultra-wide, print), halt and request operator visual check before push.
7. **CLAUDE.md ambiguity** — if a task instruction conflicts with this CLAUDE.md, or if two CLAUDE.md sections appear to conflict, halt and ask a single targeted clarifying question.

For trigger 6 specifically: HTML validation passing is necessary but not sufficient. Layout correctness is a separate quality dimension that requires human visual verification until automated visual-regression tooling is added (deferred to V2).

---

## Pre-flight checklist

Before making any changes to repository content, complete every step:

1. **Enumerate available Skills** from the system reminder. Confirm expected runtime tooling is loaded. If a Skill is unexpectedly missing or unexpectedly present, halt before proceeding.
2. **Read this CLAUDE.md authority matrix** in full. Match the proposed task against the tiers.
3. **Read `change_log.md` tail** (last 5-10 entries). Understand recent state and avoid colliding with concurrent work.
4. **Verify repository state via tooling, not assertion**: `git status`, `git log --oneline -5`, and `gh repo view` if cross-checking remote state. Do not trust operator statements about repo state without verification — operator-asserted state is a starting hypothesis, not a confirmed fact.
5. **Check working directory is clean** before starting destructive operations. If `git status` shows uncommitted changes you did not produce, halt and ask before proceeding.
6. **Confirm test backstop is available**: HTML validation tool present (`html5validator`, `pip install html5validator` if missing). For tasks touching links, confirm a link-checker is available.

---

## Site state snapshot

This section is updated post-task. Do not edit during normal task execution.

- **Current tagline (locked):** "The architecture system for Claude" (sentence case) / "The Architecture System for Claude" (title case)
- **Current main HEAD:** _populated post first commit_
- **Last deploy timestamp:** _populated post first auto-deploy_
- **Section count:** 7 (Hero, Problem, Solution, Skills, Practice, Compilation, Use Case, Footer)
- **Asset inventory:** favicons (4 sizes + ico), apple-touch-icon, og-image.png, sitemap.xml, robots.txt
- **External dependencies:** Google Fonts (Inter, JetBrains Mono); GitHub stars badge from img.shields.io
- **Halt-violation count this session:** _populated post-session_

---

## Repository structure

```
rootnode-website/
├── CLAUDE.md                        # This file
├── README.md                        # Repo overview, deploy info
├── change_log.md                    # Per-session change log
├── .gitignore                       # Standard ignores
├── .claude/
│   └── settings.json                # Permission scope
├── index.html                       # The entire site (single-page)
├── assets/                          # Brand assets (Tier 1)
│   └── (SVGs, future)
├── og-image.png
├── favicon.ico
├── favicon-16x16.png
├── favicon-32x32.png
├── favicon-48x48.png
├── apple-touch-icon.png
├── android-chrome-192x192.png
├── android-chrome-512x512.png
├── sitemap.xml
└── robots.txt
```

Single-page architecture is intentional. CSS is inline in `<style>` block within `<head>`. JS (interactive panels) is inline at bottom of `<body>`. This is the deployment unit; CF Pages serves the directory verbatim.

---

## Deployment architecture

Cloudflare Pages is connected to this repository, branch `main`, and auto-deploys on every push. No build command (static site). Build output directory is `/` (repo root). Custom domain `rootnode.design` is attached at the Pages project level.

Every push to `main` triggers an auto-deploy. Deploy verification is operator-side (visual check at rootnode.design) until automated checks are added (V2 backlog).

CC's deploy responsibility ends at `git push origin main`. Cloudflare deploy success/failure visibility is operator-side via Cloudflare dashboard. If a push triggers a deploy failure, operator surfaces the failure to the next CC session as input.

---

## Tools and verification

Available tools (assumed present; verify in pre-flight if uncertain):
- `git`, `gh` (GitHub CLI)
- `python` (Python 3.x with stdlib — `re`, `pathlib`, `zipfile`)
- `html5validator` (install via `pip install html5validator` if missing)
- Standard Unix utilities via Git Bash on Windows

**Cross-platform discipline.** Operator's environment is Windows + Git Bash. OS-specific binaries (`sed`, `zip`, BSD-vs-GNU flag variants) are not portable. Use Python stdlib for any text manipulation: `python -c "import re, pathlib; ..."` is the cross-platform default. This is the R7 lesson from Skills repo CC sessions.

**Test backstop.** At V1, HTML validation is the only automated check. Run `html5validator index.html` before any push that modifies HTML. Visual layout verification is operator-side. Link-checker, lighthouse, accessibility scanner are V2 candidates — add when their absence has caused a real defect.

---

## Naming and commit conventions

**Commit message format:**
```
<scope>: <imperative summary>

<optional body explaining why, not what>

Tier: <1|2|3>
```

Scopes: `copy`, `style`, `assets`, `meta`, `chore`, `fix`, `perf`, `a11y`, `docs`, `infra`.

Example: `copy: update tagline to "The architecture system for Claude" in 5 locations` with `Tier: 1`.

**Branch policy:** direct-to-`main` is authorized for CC sessions per operator decision. No PR workflow at V1 (single-operator repo, fast iteration prioritized). Revisit if multi-contributor or if a regression demonstrates PR review value.

**File naming:** kebab-case for new HTML/CSS/JS files. Asset files retain their existing naming convention (e.g., `android-chrome-192x192.png`).

---

## Cross-reference

**Brand decisions (tagline, voice, positioning) source-of-truth:** root.node seed Project (chat-side, Anthropic Projects). The seed Project Memory holds the canonical tagline lock and any future updates. When the seed Project updates the tagline, the change propagates here via an explicit CC session — not via inferred cross-repo sync.

**Skills repo:** `drayline/rootnode-skills` — separate CC environment, separate CLAUDE.md, separate scope. No automated cross-repo coordination. Brand-level changes that touch both repos require operator-coordinated lockstep sessions.

**GTM strategy and pricing:** Distribution project (chat-side). Out of scope for this repo. If a website task requires GTM context, halt and request operator load the GTM file in chat before continuing.

---

## Failure modes to recognize

- **Tagline drift in adjacent prose.** Refactor of nearby section copy that paraphrases the tagline ("The system for Claude architecture", "Claude's architecture system", etc.) is a regression. Locked phrasing is the locked phrasing — adjacent prose flows around it.
- **Inline-style proliferation.** Adding `style="..."` attributes to elements instead of using CSS classes erodes the design token system. New styling lands as classes against existing tokens.
- **External resource creep.** Each new font, script, or CDN reference is a deploy-time and runtime dependency. Treat additions as Tier 1 unless they replace something already present.
- **Single-file optimization that breaks deploy.** CF Pages serves the directory; operations like minification or HTML compression that break source readability without measurable runtime gain are negative-value.

---

*End of CLAUDE.md.*
