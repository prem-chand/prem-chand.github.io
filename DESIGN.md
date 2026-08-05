---
name: Prem Chand
description: Research dossier personal site — precise, restrained academic blue on a flat Inter system
colors:
  primary: "#1e40af"
  primary-light: "#3b82f6"
  primary-dark: "#1e3a8a"
  accent-wash: "#dbeafe"
  text: "#1f2937"
  text-secondary: "#4b5563"
  text-muted: "#6b7280"
  background: "#ffffff"
  surface: "#f9fafb"
  border: "#e5e7eb"
  code-bg: "#282c34"
  header-bg: "rgba(255, 255, 255, 0.95)"
  dark-primary: "#60a5fa"
  dark-primary-light: "#93c5fd"
  dark-primary-dark: "#3b82f6"
  dark-accent-wash: "#1e3a5f"
  dark-text: "#f3f4f6"
  dark-text-secondary: "#d1d5db"
  dark-text-muted: "#9ca3af"
  dark-background: "#111827"
  dark-surface: "#1f2937"
  dark-border: "#374151"
  dark-code-bg: "#0d1117"
  dark-header-bg: "rgba(17, 24, 39, 0.95)"
  rss-accent: "#f97316"
  code-fg: "#abb2bf"
  on-primary: "#ffffff"
typography:
  display:
    fontFamily: "Inter, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif"
    fontSize: "3rem"
    fontWeight: 700
    lineHeight: 1.2
    letterSpacing: "-0.03em"
  headline:
    fontFamily: "Inter, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif"
    fontSize: "2.25rem"
    fontWeight: 600
    lineHeight: 1.3
    letterSpacing: "-0.02em"
  title:
    fontFamily: "Inter, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif"
    fontSize: "1.5rem"
    fontWeight: 600
    lineHeight: 1.3
    letterSpacing: "-0.01em"
  body:
    fontFamily: "Inter, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif"
    fontSize: "1.05rem"
    fontWeight: 400
    lineHeight: 1.7
    letterSpacing: "normal"
  label:
    fontFamily: "Inter, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif"
    fontSize: "0.875rem"
    fontWeight: 500
    lineHeight: 1.5
    letterSpacing: "normal"
  mono:
    fontFamily: "'SF Mono', 'Fira Code', Consolas, monospace"
    fontSize: "0.9rem"
    fontWeight: 400
    lineHeight: 1.5
    letterSpacing: "normal"
rounded:
  xs: "3px"
  sm: "4px"
  md: "6px"
  lg: "8px"
spacing:
  xs: "0.25rem"
  sm: "0.5rem"
  md: "1rem"
  lg: "1.5rem"
  xl: "2rem"
  2xl: "3rem"
  3xl: "4rem"
  container: "1000px"
  header-height: "70px"
  main-padding-y: "4rem"
  main-padding-x: "2rem"
components:
  button-primary:
    backgroundColor: "{colors.primary}"
    textColor: "{colors.on-primary}"
    rounded: "{rounded.md}"
    padding: "0.75rem 1.5rem"
    typography: "{typography.label}"
  button-primary-hover:
    backgroundColor: "{colors.primary-dark}"
    textColor: "{colors.on-primary}"
  hero-link:
    backgroundColor: "transparent"
    textColor: "{colors.text-secondary}"
    rounded: "{rounded.md}"
    padding: "0.5rem 1rem"
  hero-link-hover:
    backgroundColor: "{colors.accent-wash}"
    textColor: "{colors.primary}"
  theme-toggle:
    backgroundColor: "transparent"
    textColor: "{colors.text-secondary}"
    rounded: "{rounded.lg}"
    padding: "0.625rem"
  theme-toggle-hover:
    backgroundColor: "{colors.surface}"
    textColor: "{colors.primary}"
  category-badge:
    backgroundColor: "{colors.accent-wash}"
    textColor: "{colors.primary}"
    rounded: "{rounded.xs}"
    padding: "0.15rem 0.5rem"
  tag:
    backgroundColor: "{colors.surface}"
    textColor: "{colors.text-secondary}"
    rounded: "{rounded.xs}"
    padding: "0.15rem 0.5rem"
  tag-hover:
    backgroundColor: "{colors.accent-wash}"
    textColor: "{colors.primary}"
  publication-year:
    backgroundColor: "{colors.accent-wash}"
    textColor: "{colors.primary}"
    rounded: "{rounded.sm}"
    padding: "0.25rem 0.75rem"
  toc:
    backgroundColor: "{colors.surface}"
    textColor: "{colors.text-secondary}"
    rounded: "{rounded.lg}"
    padding: "1.25rem 1.5rem"
  github-callout:
    backgroundColor: "{colors.surface}"
    textColor: "{colors.text-secondary}"
    rounded: "{rounded.lg}"
    padding: "0.75rem 1.25rem"
  rss-link:
    backgroundColor: "transparent"
    textColor: "{colors.text-muted}"
    rounded: "{rounded.sm}"
    padding: "0.2rem 0.5rem"
---

# Design System: Prem Chand

## Overview

**Creative North Star: "The Research Dossier"**

This site is a professional research dossier first: a place for evaluators to verify who Prem Chand is, what he has published, and whether the technical depth is real. The visual system is precise, restrained, and credible—Inter sans throughout, a single academic blue voice for links and status chips, flat paper-like surfaces separated by hairline borders, and hierarchy that does the work without flourish.

Density is comfortable for reading long technical posts (body ~1.05rem / 1.7 line-height, justified prose) while remaining scanable on home: hero identity, publication years, then recent writing. Light and dark themes share the same structure; dark mode swaps the blue toward sky-blue accents and cool gray surfaces for late reading, not a second brand.

**Confirmed rejections:** marketing SaaS aesthetics (gradient heroes, glassmorphism stacks, playful illustration, loud multi-CTA bands) and dark-academia / serif-editorial magazine drift. The dossier stays sans, flat, and blue-accented.

**Key Characteristics:**
- Single Inter typeface for UI and prose; mono only for code
- Academic Navy Blue as the sole accent voice (≤ ~10% of any surface)
- Flat surfaces + 1px borders; soft shadows only on photos/images
- Sticky frosted header; max content width 1000px
- Refined, restrained chrome—filled primary button is rare (404 / explicit actions)
- Math (KaTeX) and code blocks are first-class reading surfaces

## Colors

A cool institutional palette: white paper, gray ink hierarchy, and one Academic Navy Blue for action and emphasis. Dark theme mirrors roles with inverted neutrals and a lighter sky primary for contrast on charcoal.

### Primary
- **Academic Navy Blue** (`#1e40af`): light-theme primary—inline links, logo hover, nav hover, publication year chips, category badges, blockquote bar, primary button fill.
- **Signal Blue** (`#3b82f6`): lighter primary step used as `--primary-light` and dark-theme primary-dark.
- **Ink Navy** (`#1e3a8a`): hover/pressed primary-dark on light theme.
- **Sky Accent** (`#60a5fa`): dark-theme primary for links and emphasis on charcoal backgrounds.
- **Frost Wash** (`#dbeafe` light / `#1e3a5f` dark): soft blue tint behind year badges, category badges, and hero-link hover.

### Neutral
- **Near Black Text** (`#1f2937`): primary text, titles, logo.
- **Slate Secondary** (`#4b5563`): body prose, secondary UI labels.
- **Muted Gray** (`#6b7280`): meta, captions, footer copyright, TOC titles.
- **Paper White** (`#ffffff`): page background (light).
- **Surface Mist** (`#f9fafb`): footer, tables, TOC, callouts, code inline bg, theme-toggle hover.
- **Hairline Border** (`#e5e7eb`): dividers, header rule, card/list separators, table borders.
- **Code Charcoal** (`#282c34`): fenced code block background; foreground `#abb2bf`.
- **Dark stack:** background `#111827`, surface `#1f2937`, border `#374151`, text `#f3f4f6` / `#d1d5db` / `#9ca3af`, code `#0d1117`.
- **RSS Orange** (`#f97316`): hover-only exception for the RSS chip—not a second brand color.

### Named Rules
**The One Voice Rule.** Academic Navy Blue (and its theme-mapped primary) is the only chromatic accent for interactive and status UI. Do not introduce secondary brand hues except the RSS hover orange already in CSS.

**The Paper Stack Rule.** Backgrounds are either paper (`background`) or one step of surface mist—not multi-level colored panels. Borders mark structure; color is not used to invent depth.

## Typography

**Display Font:** Inter (system UI fallbacks)  
**Body Font:** Inter (same stack)  
**Label/Mono Font:** SF Mono / Fira Code / Consolas for code only  

**Character:** Single-family engineering dossier—tight negative letter-spacing on large titles, medium weights for structure, no display serif. Prose is slightly large and open for math-adjacent reading.

### Hierarchy
- **Display** (700, 3rem / ~2.25rem tablet / ~1.875rem phone, lh 1.2, tracking -0.03em): home hero name only.
- **Headline** (600, 2.25rem article h1 / 1.75rem h2, lh 1.3, tracking -0.02em): page and section titles; h2 gets a 1px bottom border.
- **Title** (600, 1.5rem section-title / 1.35rem h3 / 1.25rem post-title, lh 1.3): section headers and list titles; section-title uses 2px bottom border.
- **Body** (400, 1.05rem prose / 1.1rem hero description, lh 1.7–1.8): left-aligned article paragraphs at ~70ch measure; secondary color for body text.
- **Label** (500–600, 0.75–0.95rem): nav (0.95rem/500), meta (0.875rem), badges/tags (0.75rem), related-posts uppercase (1rem/600, tracking 0.06em), TOC title (0.75rem/700, tracking 0.08em uppercase).
- **Mono** (0.9rem): inline code and pre blocks.

### Named Rules
**The Single Family Rule.** Do not introduce a second display face for marketing flair. Inter owns UI and prose; mono is reserved for code.

**The Heading Hairline Rule.** Structural h2 and section titles earn a bottom border; do not substitute colored underlines or accent bars for ordinary headings (blockquote left bar is the exception).

## Layout

Single-column dossier at **max-width 1000px** (`--container-width`), horizontally centered. Sticky header height **70px** (60px ≤768px) with horizontal padding **2rem** (1.5rem tablet, 1rem phone). Main padding **4rem 2rem** desktop → **3rem 1.5rem** → **2rem 1rem**.

Vertical rhythm: hero **3rem** top / **4rem** bottom padding; sections **4rem** bottom margin; list items separated by hairline borders with **1.25–1.5rem** vertical padding. Hero is a horizontal flex (content + 220×280 profile photo, **3rem** gap); stacks column-reverse and centers below 768px. About photo floats right on desktop, centers on small screens.

Breakpoints observed: **768px** (primary reflow), **480px** (nav denser, hero links stack). Article images cap at **600px** wide, centered. No multi-column content grid except optional skills-grid (`auto-fit`, min 200px)—not a core marketing layout.

### Named Rules
**The 1000px Rule.** Content lives in one readable column ≤1000px. Do not expand to full-bleed marketing widths or multi-column hero showcases.

## Elevation & Depth

**Flat with hairline borders.** Depth is conveyed by (1) 1px borders, (2) one surface tone step, and (3) optional frosted sticky header (`backdrop-filter: blur(10px)` on semi-transparent header bg). Soft shadows appear only on profile photos and article images—not on cards, lists, or buttons.

### Shadow Vocabulary
- **Photo lift** (`box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1)` light / `0.3` dark): profile photo only.
- **Figure lift** (`box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1)` light / `0.3` dark): article images; dark mode also uses `opacity: 0.9` on images.

### Named Rules
**The Flat-By-Default Rule.** Surfaces are flat at rest. Do not add hover card shadows or layered glass panels. Shadows stay on photographic content.

## Shapes

Modest, utilitarian rounding—never pill-shaped marketing chrome.

- **xs (3px):** category badges, tags  
- **sm (4px):** inline code, publication year, RSS chip  
- **md (6px):** hero links, primary button  
- **lg (8px):** theme toggle, profile photo, pre blocks, blockquote outer, TOC, GitHub callout, article images  

Borders are 1px solid hairline; profile photo uses a **3px** border. Blockquotes: **3px** left bar in primary + right-side **8px** radius on surface fill.

### Named Rules
**The Quiet Corner Rule.** Prefer 3–8px radii. Avoid full pills, oversized cards, or decorative clipping paths.

## Components

Chrome is refined and restrained: most actions are text links or ghost chips; filled primary is exceptional.

### Buttons
- **Shape:** gently rounded (6px)
- **Primary (`.btn`):** Academic Navy fill, white text, padding 0.75rem 1.5rem, weight 500; hover → Ink Navy. Used sparingly (e.g. 404 home).
- **Hero link:** transparent, secondary text, 0.5rem 1rem, 6px radius, icon 18px; hover → primary text on Frost Wash (no underline).
- **Theme toggle:** icon button, 8px radius, transparent; hover → surface + primary icon color.

### Chips / Badges
- **Category badge:** primary text on Frost Wash, 3px radius, 0.75rem/600—status, not a button.
- **Tag:** surface + border, secondary text; hover primary + wash + primary border.
- **Publication year:** same language as category badge with slightly more padding (4px radius).
- **RSS link:** bordered muted chip; hover uses RSS orange (exception).

### Cards / Containers
- **Lists (posts, publications):** no card shells—hairline dividers between rows.
- **TOC / GitHub callout:** surface background, 1px border, 8px radius, internal padding ~1.25–1.5rem.
- **Code pre:** code charcoal, 8px radius, padding 1.25rem 1.5rem; no border.
- **Blockquote:** surface + primary left bar.

### Inputs / Fields
- No form controls in the current system. Prefer bordered surface fields with primary focus ring if added later—do not invent material-filled fields without a document refresh.

### Navigation
- Sticky header: logo 1.25rem/700 tracking -0.02em; horizontal nav links 0.95rem/500 secondary; gap 2.5rem (tightens on small screens). Hover → primary. No active underline style currently—keep chrome minimal.
- Footer: centered icon links (22px) + copyright muted; surface background + top border.

### Signature Components
- **Hero dossier block:** name (display) + role/company subtitle + short professional claim + social hero-links + portrait with 3px border and photo lift.
- **Publication row:** year chip | title (text color, primary on hover) + authors + italic venue.
- **Post list row:** meta date · title · excerpt; title uses text color, primary on hover without underline.
- **Related posts:** uppercase muted label, title/meta flex rows.
- **GitHub callout:** inline repo pointer with icon for posts that ship code.

### Motion
- Global transition: **0.2s ease** on color/background/border interactions.
- `html { scroll-behavior: smooth; }` for in-page anchors (TOC).
- No entrance animations or parallax.

## Do's and Don'ts

### Do:
- **Do** keep content within the 1000px column and preserve sticky header + single Inter stack.
- **Do** use Academic Navy Blue (theme-mapped primary) only for links, hovers, badges, year chips, blockquote bar, and rare primary buttons.
- **Do** separate sections and list rows with hairline borders and surface steps, not shadows.
- **Do** treat math (KaTeX) and code blocks as first-class: keep monokai/code charcoal readable and equations unobstructed.
- **Do** support light/dark via the existing CSS variable roles (`data-theme="dark"`).
- **Do** aim for WCAG 2.1 AA contrast on text and interactive states (product requirement).
- **Do** keep keyboard focus visible (`:focus-visible` primary ring), a skip link, and current-page nav indication.

### Don't:
- **Don't** introduce gradient heroes, glassmorphism stacks, blob illustrations, or multi-CTA marketing bands.
- **Don't** add a display serif or “editorial magazine” second typeface.
- **Don't** invent a second brand color family (beyond the existing RSS orange hover exception).
- **Don't** put box-shadows on list rows, cards, or buttons; shadows stay on photos/figures.
- **Don't** widen to full-bleed portfolio galleries or break the flat paper reading surface for long posts.
- **Don't** fabricate visual proof (fake metrics, logo walls, testimonial cards) that product truth forbids.
