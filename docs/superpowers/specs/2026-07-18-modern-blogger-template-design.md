# Modern Blogger Template — Design Spec

**Date:** 2026-07-18
**Target blog:** https://michaelkpate.blogspot.com/
**Deliverable:** `moderntemplate.xml` — a single self-contained Blogger theme file, installed by pasting into Blogger's Theme → Edit HTML.

## Goals

- A modern, clean template for a personal blog about writing and coding projects.
- Old posts (2004–2014) remain untouched and must render acceptably.
- No external JS libraries; only Google Fonts as an external dependency.
- Visual language adapted from `DESIGN-claude.md` (warm-editorial cream/coral/dark-navy system).

## Decisions (settled during brainstorming)

| Decision | Choice |
|---|---|
| Engine | Blogger Layouts **v3** (`b:layoutsVersion='3'`, widget version 2), built from scratch; `Contempo theme.xml` in repo root is a syntax reference only |
| Layout | Single centered content column (~44rem max) |
| Navigation | Contempo-style **slide-out drawer** (left), behind a menu button |
| Homepage | **Snippet list** — title, date, excerpt per post (`data:post.snippets`) |
| Features | Dark mode (auto + manual toggle), styled code blocks with copy button, Blogger comments, search + labels |
| Branding | Borrow the Claude palette/voice; **no** Anthropic spike-mark or wordmark |

## Architecture

One XML file with:

- `<head>`: `all-head-content`, canonical link, per-page-type `<title>`, Open Graph + Twitter card meta, JSON-LD `BlogPosting`/`Blog` schema, Google Fonts `<link>` (Cormorant Garamond 500/600, Inter 400/500, JetBrains Mono 400), theme-color meta.
- `<b:skin>`: all CSS, driven by custom properties on `:root` with a `[data-theme]` override for the manual toggle and `prefers-color-scheme` as default.
- Body structure:
  - **Header** (sticky, canvas background, hairline bottom border): menu button (opens drawer) · blog title (Cormorant Garamond) · theme toggle button.
  - **Drawer** (`<aside>`, transform: translateX off-canvas + overlay): a `b:section id='drawer'` containing default widgets — BlogSearch, Profile (About), Labels, BlogArchive. Rearrangeable via Layout dashboard. Closes on overlay click or Escape; focus is moved into the drawer on open.
  - **Main** (`b:section id='main'`, single `Blog1` widget, locked): index views render snippet cards; item/static pages render full post + comments.
  - **Footer** (dark surface `#181715`, cream text): copyright with dynamic year, attribution line, feed link.
- Inline `<script>` (end of body, vanilla JS, ~60 lines): drawer open/close, theme toggle with `localStorage` persistence (key `theme`), copy-button injection on `pre` blocks.

## Visual system (from DESIGN-claude.md)

**Light theme:** canvas `#faf9f5`, ink `#141413`, body text `#3d3d3a`, muted `#6c6a64`, cards/drawer `#efe9de`, hairline `#e6dfd8`, accent coral `#cc785c` (links, buttons, focus rings; active `#a9583e`).

**Dark theme:** canvas `#181715`, elevated surfaces (drawer, cards) `#252320`, text `#faf9f5`, secondary text `#a09d96`, same coral accent, hairline `rgba(250,249,245,0.12)`.

**Code blocks (both themes):** background `#181715` (dark canvas uses `#1f1e1b` inner tone), JetBrains Mono 14px/1.6, 12px radius, horizontal scroll (no wrap), copy button top-right. Lightweight CSS classes for syntax colors (muted blues/oranges per the doc's code-window treatment); tokenization is manual (author writes `<span class>`s or pastes pre-highlighted HTML) — no highlighter library. Plain `<pre>` with no spans still looks good (cream-on-navy monospace).

**Typography:** Cormorant Garamond 500, negative tracking, weight-not-bold for blog title and h1–h3 (post titles ~36px item / ~28px snippet card); Inter 400 for body at 17px/1.55, 500 for labels/buttons/nav; JetBrains Mono for code. System-stack fallbacks throughout.

**Shape/spacing:** 8px radius buttons/inputs, 12px cards/code, pill label badges (`#efe9de` bg / ink text; dark: `#252320`/cream), hairline borders instead of shadows, 4px-base spacing scale, generous vertical rhythm (~48–96px between major blocks).

## Page types (via `data:view` conditionals)

- **Homepage/index/label/archive/search:** heading (blog description on homepage; "Posts labeled X" etc. otherwise), snippet cards — post title (serif, links to post), date, `data:post.snippets.long`, label pills. Older/newer pagination links.
- **Item (post):** title, date + comment-count meta line, full post body, label pills, Blogger comment section (styled to match), prev/next links.
- **Static page:** title + body, no post meta/comments unless enabled.
- **Error page:** friendly 404 with search box and homepage link.

## SEO / meta

Keep: `all-head-content`, canonical, per-page titles (`Post — Blog Title` pattern), meta description from `data:blog.metaDescription` / post snippet, Open Graph (type article/website, image from `data:blog.postImageUrl` when present), Twitter summary card, JSON-LD BlogPosting on item pages (headline, datePublished, dateModified, author, image). Drop entirely: legacy crawler meta tags, IE conditionals, revisit-after, etc.

## Old-post compatibility

Post body CSS constrains legacy content: `img {max-width:100%; height:auto}`, tables scroll horizontally in a wrapper style, floats cleared, old inline font styling inherits the new body font where possible (`.post-body font { ... }` normalization is best-effort, not guaranteed).

## Error handling / robustness

- JS is progressive enhancement: with JS disabled, drawer is reachable via a `:target`-free fallback (drawer content also linked from footer nav: search page, archive), theme follows `prefers-color-scheme`, code blocks simply lack copy buttons.
- All widgets except Blog1 are unlocked so the user can add/remove from the Layout dashboard without editing XML.
- Template validates against Blogger's importer (correct namespaces, escaped entities, CDATA for skin).

## Testing

1. XML well-formedness check locally.
2. Install on the live blog (Blogger keeps a backup; we also keep the current theme exported in-repo before switching — user action).
3. Manual pass: homepage, an old post (2004-era), a code-heavy test post, label page, archive page, search, 404; both themes; mobile (375px) and desktop widths.
4. Validate structured data with Google Rich Results test; OG with a card debugger.

## Out of scope

- Migrating/editing old post content.
- Automatic syntax tokenization (highlighter library) — revisit later if manual classes prove annoying.
- Related-posts, newsletter signup, analytics snippets (user can add via widgets/gadgets).
