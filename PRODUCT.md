# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Users

Primary audience: hiring managers, collaborators, and peers evaluating Prem Chand as a robotics / controls professional. They arrive to answer “who is this person, what have they shipped or published, and is the technical depth credible?”

Secondary audience: researchers, engineers, and students who open individual posts for technical learning (math + code). Writing supports credibility and knowledge-sharing; it is not the site’s primary job.

## Product Purpose

Personal professional site for Prem Chand: establish identity and credibility through role context, peer-reviewed publications, and a modest body of technical notes on control systems, optimization, reinforcement learning, and robotics.

Success means a visitor can quickly understand who Prem is (Controls Team Lead at Strider Robotics, legged robots / sim-to-real RL), verify research credentials via publications and PDFs, and optionally dig into writing that demonstrates technical depth—without needing LinkedIn or a CV as the only source of truth.

## Positioning

A research portfolio with light technical notes—not a generic robotics blog and not a pure content site. Authority comes from published work (ICRA, RA-L) and real industry work on getting RL policies to survive hardware (actuators, test infrastructure, sim-to-real), with occasional first-principles posts that reinforce that depth rather than replace the portfolio.

## Operating Context

- Public site: https://prem-chand.github.io/
- Static Hugo site, deployed from `master` via GitHub Actions to GitHub Pages
- Surfaces: Home (hero + publications highlight + recent writing), Publications (full list + PDF downloads), Posts (articles and series), About (bio, background, interests)
- Reading workflows: scan bio/role → open publications or PDFs → optionally browse post series (e.g. Tabular RL)
- Authoring workflow: Markdown under `content/`, math via Goldmark passthrough, local preview with Hugo, push to deploy
- Related public artifacts: GitHub (`prem-chand`), e.g. companion repos linked from posts (QuadrupedMPC)

## Capabilities and Constraints

**Capabilities**
- Professional identity: name, role, company, location, contact and social links (GitHub, Twitter/X, LinkedIn, email)
- Publications listing with downloadable PDFs under `static/publications/`
- Blog posts and series (categories/tags, RSS for posts, TOC on longer posts)
- Math-heavy technical writing (inline/block LaTeX via Hugo markup passthrough)
- Profile photo and about narrative
- Custom layouts and CSS (no theme submodule currently in use)

**Constraints**
- Stay on Hugo + GitHub Pages (Hugo Extended as used in CI; currently ~0.153.x)
- Math remains first-class in posts (do not design reading surfaces that break or hide equations)
- Preserve existing content, series structure, publication PDFs, profile photo, and confirmed bio/employer facts unless Prem changes them
- Never invent employers, papers, testimonials, metrics, awards, or other credentials not already on the site or provided by Prem
- Accessibility: WCAG 2.1 AA for public pages

**Undecided / open**
- Whether home should emphasize publications vs writing when both compete for attention (default: professional presence first)
- Future surfaces beyond Home / Publications / Posts / About (none confirmed)

## Brand Commitments

- Name: Prem Chand
- Role/company copy as on site: Controls Team Lead at Strider Robotics (Bengaluru, India)
- Voice on existing pages: direct, technically precise, first-person; favors plain language about hard engineering (“survive contact with reality”) over marketing hype
- Identity assets on hand: `static/images/profile.jpg`; publication PDFs; post diagrams (e.g. convolution figures, MPC figure)
- Domain/identity: prem-chand.github.io; author email and socials as in `hugo.toml`

## Evidence on Hand

- Peer-reviewed publications with full PDFs:
  - Interactive Dynamic Walking… (RA-L 2022)
  - An Adaptive Supervisory Control Approach… (ICRA 2020)
- About copy covering Strider Robotics work, DRAIL Lab / University of Delaware MS, IIT Bombay B.Tech, prior actuarial year
- Technical posts and series (e.g. Tabular RL, MPC for quadrupeds, optimizers, foundations of convolution)
- Profile photo at `static/images/profile.jpg`
- Site description in `hugo.toml`: robotics AI / RL / sim-to-real / legged locomotion

**Must not fabricate:** customer logos, hiring outcomes, download counts, “featured in” claims, testimonials, new publications, or performance claims about robots or policies beyond what content already states.

## Product Principles

1. **Credibility before volume** — Professional facts and publications earn trust first; writing depth supports them, never substitutes with unearned claims.
2. **Truthful and complete identity** — Role, affiliations, papers, and contact paths stay accurate, findable, and unembellished.
3. **Math and code as proof of craft** — When technical content appears, equations and implementation detail are first-class, not decoration.
4. **Scanable portfolio, optional depth** — A first-time evaluator should leave with a clear professional picture in one visit; series and long posts reward those who dig in.
5. **Accessible by default** — Meet WCAG 2.1 AA so content and credentials are usable without special knowledge of the site’s structure.

## Accessibility & Inclusion

Target **WCAG 2.1 AA** for public pages: sufficient contrast, keyboard operability, semantic structure, meaningful alternative text for informative images, and visible focus states.
