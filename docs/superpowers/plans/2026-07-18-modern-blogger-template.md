# Modern Blogger Template Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build `moderntemplate.xml`, a self-contained Blogger v3 theme (warm cream/coral/dark-navy editorial design) for michaelkpate.blogspot.com.

**Architecture:** Single XML theme file built incrementally: skeleton + head → CSS skin → Blog1 widget markup → drawer navigation → inline JS. Each task ends with an XML well-formedness check and a commit. `Contempo theme.xml` in the repo root is the syntax reference for v3 includable names — never copy its styling.

**Tech Stack:** Blogger Layouts v3 (`b:layoutsVersion='3'`, widget version 2), CSS custom properties, vanilla JS, Google Fonts (Cormorant Garamond, Inter, JetBrains Mono).

## Global Constraints

- Spec: `docs/superpowers/specs/2026-07-18-modern-blogger-template-design.md` — read it before starting any task.
- One deliverable file: `moderntemplate.xml` at repo root. No build step.
- No external JS libraries; only Google Fonts as external dependency.
- Colors verbatim from spec: light canvas `#faf9f5`, ink `#141413`, body `#3d3d3a`, muted `#6c6a64`, card `#efe9de`, hairline `#e6dfd8`, coral `#cc785c`, coral-active `#a9583e`; dark canvas `#181715`, dark elevated `#252320`, dark text `#faf9f5`, dark secondary `#a09d96`, code inner `#1f1e1b`.
- Typography: Cormorant Garamond 500/600 display (never bold-700, negative tracking), Inter 400/500 body/UI at 17px/1.55, JetBrains Mono 400 code at 14px/1.6.
- Radii: 8px buttons/inputs, 12px cards/code, 9999px pills. Hairline borders, no shadows.
- No Anthropic spike-mark/wordmark anywhere.
- Well-formedness check after every task: `[xml](Get-Content -Raw moderntemplate.xml) > $null; if ($?) { "XML OK" }` in PowerShell. Expected output: `XML OK`.
- Commit after every task with a `feat:`/`fix:` message ending in the Claude co-author trailer.

---

### Task 1: Skeleton and head

**Files:**
- Create: `moderntemplate.xml`

**Interfaces:**
- Produces: root element with v3 attributes; `<head>` complete; empty `<b:skin>` placeholder comment `/* SKIN */`; body landmarks `header.site-header`, `aside#drawer`, `div.drawer-overlay`, `main.site-main` containing `b:section id='main'` with locked Blog1 widget stub, `footer.site-footer`; `b:section id='drawer-widgets'` inside the drawer. Later tasks fill skin CSS (Task 2), Blog1 includables (Task 3), drawer widgets (Task 4), scripts (Task 5).

- [x] **Step 1: Write the full skeleton**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE html>
<html b:css='false' b:defaultwidgetversion='2' b:layoutsVersion='3' b:responsive='true' expr:dir='data:blog.languageDirection' expr:lang='data:blog.locale' xmlns='http://www.w3.org/1999/xhtml' xmlns:b='http://www.google.com/2005/gml/b' xmlns:data='http://www.google.com/2005/gml/data' xmlns:expr='http://www.google.com/2005/gml/expr'>
  <head>
    <meta charset='UTF-8'/>
    <meta content='width=device-width, initial-scale=1' name='viewport'/>
    <meta content='#faf9f5' media='(prefers-color-scheme: light)' name='theme-color'/>
    <meta content='#181715' media='(prefers-color-scheme: dark)' name='theme-color'/>
    <b:include data='blog' name='all-head-content'/>

    <!-- Titles per page type -->
    <b:if cond='data:view.isHomepage'>
      <title><data:blog.title.escaped/></title>
    <b:elseif cond='data:view.isError'/>
      <title>Page not found &#8212; <data:blog.title.escaped/></title>
    <b:else/>
      <title><data:view.title.escaped/> &#8212; <data:blog.title.escaped/></title>
    </b:if>

    <link expr:href='data:view.url.canonical' rel='canonical'/>
    <b:if cond='data:view.description'>
      <meta expr:content='data:view.description.escaped' name='description'/>
    </b:if>

    <!-- Open Graph + Twitter -->
    <meta expr:content='data:blog.title.escaped' property='og:site_name'/>
    <meta expr:content='data:view.title.escaped' property='og:title'/>
    <meta expr:content='data:view.url.canonical' property='og:url'/>
    <b:if cond='data:view.description'>
      <meta expr:content='data:view.description.escaped' property='og:description'/>
    </b:if>
    <b:if cond='data:view.isSingleItem'>
      <meta content='article' property='og:type'/>
    <b:else/>
      <meta content='website' property='og:type'/>
    </b:if>
    <b:if cond='data:view.featuredImage'>
      <meta expr:content='resizeImage(data:view.featuredImage, 1200, &quot;1200:630&quot;)' property='og:image'/>
      <meta content='summary_large_image' name='twitter:card'/>
    <b:else/>
      <meta content='summary' name='twitter:card'/>
    </b:if>
    <meta expr:content='data:view.title.escaped' name='twitter:title'/>

    <!-- Fonts -->
    <!-- (JSON-LD BlogPosting lives in the Blog1 item view — Task 3 — where data:post is in scope) -->
    <link href='https://fonts.googleapis.com' rel='preconnect'/>
    <link crossorigin='anonymous' href='https://fonts.gstatic.com' rel='preconnect'/>
    <link href='https://fonts.googleapis.com/css2?family=Cormorant+Garamond:wght@500;600&amp;family=Inter:wght@400;500&amp;family=JetBrains+Mono&amp;display=swap' rel='stylesheet'/>

    <b:skin><![CDATA[
    /* SKIN */
    ]]></b:skin>
  </head>
  <body>
    <a class='skip-link' href='#main-content'>Skip to content</a>
    <header class='site-header'>
      <button aria-controls='drawer' aria-expanded='false' aria-label='Open menu' class='icon-btn' id='menu-btn' type='button'>&#9776;</button>
      <a class='site-title' expr:href='data:blog.homepageUrl'><data:blog.title/></a>
      <button aria-label='Toggle dark mode' class='icon-btn' id='theme-btn' type='button'>&#9789;</button>
    </header>

    <div class='drawer-overlay' id='drawer-overlay'></div>
    <aside aria-label='Site navigation' class='drawer' id='drawer'>
      <b:section id='drawer-widgets' name='Drawer' showaddelement='yes'>
      </b:section>
    </aside>

    <main class='site-main' id='main-content'>
      <b:section class='main' id='main' showaddelement='no'>
        <b:widget id='Blog1' locked='true' title='Blog Posts' type='Blog' version='2'>
        </b:widget>
      </b:section>
    </main>

    <footer class='site-footer'>
      <p>&#169; <span id='year'></span> <a expr:href='data:blog.homepageUrl'><data:blog.title/></a></p>
      <p class='footer-links'>
        <a expr:href='data:blog.homepageUrl + &quot;search&quot;'>Search</a> &#183;
        <a expr:href='data:blog.homepageUrl + &quot;feeds/posts/default&quot;'>Feed</a>
      </p>
    </footer>
    <!-- SCRIPTS -->
  </body>
</html>
```

- [x] **Step 2: Verify well-formedness**

Run: `[xml](Get-Content -Raw moderntemplate.xml) > $null; if ($?) { "XML OK" }`
Expected: `XML OK`

- [ ] **Step 3: Commit**

`git add moderntemplate.xml` then commit `feat: add v3 template skeleton with head, meta, and body landmarks`.

---

### Task 2: CSS skin (design system)

**Files:**
- Modify: `moderntemplate.xml` — replace `/* SKIN */` inside the `<b:skin>` CDATA.

**Interfaces:**
- Consumes: class names from Task 1 (`site-header`, `site-title`, `icon-btn`, `drawer`, `drawer-overlay`, `site-main`, `site-footer`, `skip-link`).
- Produces: CSS custom properties on `:root` / `[data-theme='dark']`; classes used by Task 3 (`post-card`, `post-card-title`, `post-meta`, `post-snippet`, `label-pill`, `post`, `post-title`, `post-body`, `pager`, `error-page`) and Task 4 (`drawer.open`, `drawer-overlay.visible`).

- [x] **Step 1: Write the skin CSS**

Replace `/* SKIN */` with the following (inside CDATA, so no escaping needed):

```css
/* ---- Tokens ---- */
:root {
  --canvas:#faf9f5; --ink:#141413; --body:#3d3d3a; --muted:#6c6a64;
  --card:#efe9de; --hairline:#e6dfd8; --accent:#cc785c; --accent-active:#a9583e;
  --code-bg:#181715; --code-inner:#1f1e1b; --on-dark:#faf9f5; --on-dark-soft:#a09d96;
  --serif:'Cormorant Garamond',Garamond,'Times New Roman',serif;
  --sans:'Inter',-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,sans-serif;
  --mono:'JetBrains Mono',ui-monospace,Consolas,monospace;
  --r-btn:8px; --r-card:12px;
}
@media (prefers-color-scheme: dark) { :root:not([data-theme='light']) {
  --canvas:#181715; --ink:#faf9f5; --body:#faf9f5; --muted:#a09d96;
  --card:#252320; --hairline:rgba(250,249,245,.12);
} }
:root[data-theme='dark'] {
  --canvas:#181715; --ink:#faf9f5; --body:#faf9f5; --muted:#a09d96;
  --card:#252320; --hairline:rgba(250,249,245,.12);
}

/* ---- Base ---- */
* { box-sizing:border-box; margin:0; padding:0; }
html { scroll-behavior:smooth; }
body { background:var(--canvas); color:var(--body); font:400 17px/1.55 var(--sans); }
a { color:var(--accent); text-decoration:none; }
a:hover, a:focus { color:var(--accent-active); text-decoration:underline; }
h1,h2,h3 { font-family:var(--serif); font-weight:500; color:var(--ink); letter-spacing:-.02em; line-height:1.15; }
.skip-link { position:absolute; left:-9999px; }
.skip-link:focus { left:8px; top:8px; background:var(--accent); color:#fff; padding:8px 14px; border-radius:var(--r-btn); z-index:60; }

/* ---- Header ---- */
.site-header { position:sticky; top:0; z-index:40; display:flex; align-items:center; gap:16px;
  height:64px; padding:0 20px; background:var(--canvas); border-bottom:1px solid var(--hairline); }
.site-title { font-family:var(--serif); font-size:26px; font-weight:600; color:var(--ink); letter-spacing:-.02em; margin-right:auto; }
.site-title:hover { color:var(--accent); text-decoration:none; }
.icon-btn { width:40px; height:40px; border:1px solid var(--hairline); border-radius:9999px;
  background:var(--canvas); color:var(--ink); font-size:18px; cursor:pointer; }
.icon-btn:hover { border-color:var(--accent); color:var(--accent); }

/* ---- Drawer ---- */
.drawer { position:fixed; top:0; left:0; bottom:0; width:300px; max-width:85vw; z-index:50;
  background:var(--card); padding:24px 20px; overflow-y:auto;
  transform:translateX(-100%); transition:transform .25s ease; }
.drawer.open { transform:none; }
.drawer-overlay { position:fixed; inset:0; z-index:45; background:rgba(20,20,19,.4);
  opacity:0; pointer-events:none; transition:opacity .25s ease; }
.drawer-overlay.visible { opacity:1; pointer-events:auto; }
.drawer .widget { margin-bottom:32px; }
.drawer h3, .drawer .title { font-family:var(--sans); font-size:12px; font-weight:500;
  letter-spacing:1.5px; text-transform:uppercase; color:var(--muted); margin-bottom:12px; }
.drawer ul { list-style:none; }
.drawer li { padding:4px 0; }

/* ---- Layout ---- */
.site-main { max-width:44rem; margin:0 auto; padding:48px 20px 96px; }

/* ---- Snippet cards (index) ---- */
.post-card { background:var(--card); border-radius:var(--r-card); padding:32px; margin-bottom:24px; }
.post-card-title { font-size:28px; margin-bottom:8px; }
.post-card-title a { color:var(--ink); }
.post-card-title a:hover { color:var(--accent); text-decoration:none; }
.post-meta { font-size:13px; font-weight:500; color:var(--muted); margin-bottom:12px; }
.post-snippet { color:var(--body); }
.label-pill { display:inline-block; background:var(--canvas); color:var(--ink); font-size:13px;
  font-weight:500; border:1px solid var(--hairline); border-radius:9999px; padding:4px 12px; margin:12px 6px 0 0; }
.label-pill:hover { border-color:var(--accent); color:var(--accent); text-decoration:none; }

/* ---- Full post (item) ---- */
.post-title { font-size:36px; margin-bottom:8px; }
.post-body { margin-top:24px; }
.post-body p, .post-body ul, .post-body ol, .post-body blockquote { margin-bottom:1.1em; }
.post-body ul, .post-body ol { padding-left:1.4em; }
.post-body h2 { font-size:28px; margin:1.4em 0 .5em; }
.post-body h3 { font-size:22px; margin:1.2em 0 .5em; }
.post-body blockquote { border-left:3px solid var(--accent); padding-left:16px; color:var(--muted); }
.post-body img { max-width:100%; height:auto; border-radius:var(--r-card); }
.post-body .table-wrap, .post-body table { max-width:100%; overflow-x:auto; display:block; }

/* ---- Code ---- */
.post-body pre, .post-body code { font-family:var(--mono); font-size:14px; line-height:1.6; }
.post-body code { background:var(--card); padding:2px 6px; border-radius:4px; }
.post-body pre { position:relative; background:var(--code-bg); color:var(--on-dark);
  border-radius:var(--r-card); padding:24px; overflow-x:auto; margin-bottom:1.1em; }
.post-body pre code { background:transparent; padding:0; color:inherit; }
.copy-btn { position:absolute; top:10px; right:10px; font:500 12px var(--sans);
  background:#252320; color:var(--on-dark-soft); border:1px solid rgba(250,249,245,.15);
  border-radius:var(--r-btn); padding:5px 10px; cursor:pointer; }
.copy-btn:hover { color:var(--on-dark); border-color:var(--accent); }
/* manual syntax classes */
.tok-k { color:#cc785c; } .tok-s { color:#5db8a6; } .tok-c { color:#8e8b82; font-style:italic; }
.tok-n { color:#e8a55a; } .tok-f { color:#a9c4e8; }

/* ---- Pager, comments, error ---- */
.pager { display:flex; justify-content:space-between; gap:16px; margin-top:48px; font-weight:500; }
.comments { margin-top:64px; border-top:1px solid var(--hairline); padding-top:32px; }
.error-page { text-align:center; padding:64px 0; }
.error-page h1 { font-size:48px; margin-bottom:16px; }

/* ---- Footer ---- */
.site-footer { background:var(--code-bg); color:var(--on-dark-soft); text-align:center;
  padding:48px 20px; font-size:14px; }
.site-footer a { color:var(--on-dark); }
.footer-links { margin-top:8px; }

/* ---- Legacy post content normalization ---- */
.post-body font { font-family:inherit; color:inherit; }
.post-body [style*='float'] { max-width:50%; }
.post-body:after { content:''; display:block; clear:both; }

@media (max-width:600px) {
  .post-card { padding:20px; }
  .post-title { font-size:28px; }
  .post-card-title { font-size:22px; }
  .site-main { padding-top:24px; }
}
```

- [x] **Step 2: Verify well-formedness** — same command as Task 1, expected `XML OK`.

- [ ] **Step 3: Commit** — `feat: add design-system skin CSS (cream/coral/dark-navy, dark mode tokens)`.

---

### Task 3: Blog1 widget markup

**Files:**
- Modify: `moderntemplate.xml` — fill the empty `<b:widget id='Blog1' ...>` element.

**Interfaces:**
- Consumes: CSS classes from Task 2.
- Produces: complete post rendering for all `data:view` types. Uses built-in v2 Blog-widget includables `commentPicker`, `previousPageLink`/`nextPageLink` (verified present in `Contempo theme.xml:3578,3646`) so comments and pagination need no custom internals.

- [x] **Step 1: Write the widget includables**

Inside `<b:widget id='Blog1' locked='true' title='Blog Posts' type='Blog' version='2'>`:

```xml
<b:includable id='main'>
  <b:if cond='data:view.isError'>
    <div class='error-page'>
      <h1>Page not found</h1>
      <p>The page you were looking for does not exist.</p>
      <p><a expr:href='data:blog.homepageUrl'>Go to the homepage</a></p>
    </div>
  <b:else/>
    <b:if cond='data:view.isMultipleItems'>
      <b:if cond='data:view.isHomepage'>
        <b:if cond='data:blog.metaDescription'>
          <p class='post-meta'><data:blog.metaDescription/></p>
        </b:if>
      <b:else/>
        <h1 class='post-card-title'><data:view.title.escaped/></h1>
      </b:if>
      <b:loop values='data:posts' var='post'>
        <article class='post-card'>
          <h2 class='post-card-title'><a expr:href='data:post.url'><data:post.title/></a></h2>
          <p class='post-meta'><data:post.timestamp/></p>
          <p class='post-snippet'><data:post.snippets.long/></p>
          <b:if cond='data:post.labels'>
            <div>
              <b:loop values='data:post.labels' var='label'>
                <a class='label-pill' expr:href='data:label.url'><data:label.name/></a>
              </b:loop>
            </div>
          </b:if>
        </article>
      </b:loop>
      <div class='pager'>
        <b:if cond='data:newerPageUrl'><a expr:href='data:newerPageUrl'>&#8592; Newer posts</a><b:else/><span/></b:if>
        <b:if cond='data:olderPageUrl'><a expr:href='data:olderPageUrl'>Older posts &#8594;</a></b:if>
      </div>
    <b:else/>
      <b:loop values='data:posts' var='post'>
        <article class='post'>
          <h1 class='post-title'><data:post.title/></h1>
          <p class='post-meta'>
            <data:post.timestamp/>
            <b:if cond='data:view.isPost and data:post.allowComments'>
              &#183; <a expr:href='data:post.url + &quot;#comments&quot;'><data:post.numberOfComments/> comments</a>
            </b:if>
          </p>
          <div class='post-body'><data:post.body/></div>
          <b:if cond='data:post.labels'>
            <div>
              <b:loop values='data:post.labels' var='label'>
                <a class='label-pill' expr:href='data:label.url'><data:label.name/></a>
              </b:loop>
            </div>
          </b:if>
          <b:if cond='data:view.isPost'>
            <script type='application/ld+json'>
            {
              &quot;@context&quot;: &quot;https://schema.org&quot;,
              &quot;@type&quot;: &quot;BlogPosting&quot;,
              &quot;mainEntityOfPage&quot;: &quot;<data:post.url.canonical/>&quot;,
              &quot;headline&quot;: &quot;<data:post.title.escaped/>&quot;,
              &quot;url&quot;: &quot;<data:post.url.canonical/>&quot;,
              &quot;datePublished&quot;: &quot;<data:post.date.iso8601/>&quot;,
              &quot;dateModified&quot;: &quot;<data:post.lastUpdated.iso8601/>&quot;,
              &quot;author&quot;: { &quot;@type&quot;: &quot;Person&quot;, &quot;name&quot;: &quot;<data:post.author.name/>&quot; }<b:if cond='data:post.featuredImage'>,
              &quot;image&quot;: &quot;<data:post.featuredImage/>&quot;</b:if>
            }
            </script>
          </b:if>
          <b:include cond='data:view.isSingleItem' data='post' name='commentPicker'/>
        </article>
      </b:loop>
    </b:if>
  </b:if>
</b:includable>
```

Note: `commentPicker` and its comment-rendering internals are default includables of the version-2 Blog widget — do not define them. If Blogger's importer rejects the reference, grep `Contempo theme.xml` for `commentPicker` and copy its `commentPicker`/`comments`/`commentList`/`commentForm` includables verbatim as a fallback.

- [x] **Step 2: Verify well-formedness** — expected `XML OK`.

- [ ] **Step 3: Commit** — `feat: add Blog1 widget markup for index, item, and error views`.

---

### Task 4: Drawer widgets

**Files:**
- Modify: `moderntemplate.xml` — fill `b:section id='drawer-widgets'`.

**Interfaces:**
- Consumes: `.drawer` CSS from Task 2.
- Produces: four unlocked widgets with default (built-in) includables — search, about, labels, archive.

- [x] **Step 1: Add the widgets**

Inside the `drawer-widgets` section:

```xml
<b:widget id='BlogSearch1' locked='false' title='Search' type='BlogSearch' version='2'/>
<b:widget id='Profile1' locked='false' title='About' type='Profile' version='2'/>
<b:widget id='Label1' locked='false' title='Labels' type='Label' version='2'/>
<b:widget id='BlogArchive1' locked='false' title='Archive' type='BlogArchive' version='2'/>
```

Leave all widget bodies empty — Blogger supplies default v2 includables on import.

- [x] **Step 2: Verify well-formedness** — expected `XML OK`.

- [ ] **Step 3: Commit** — `feat: add drawer widgets (search, about, labels, archive)`.

---

### Task 5: Inline JS (drawer, theme toggle, copy buttons, year)

**Files:**
- Modify: `moderntemplate.xml` — replace `<!-- SCRIPTS -->` before `</body>`.

**Interfaces:**
- Consumes: ids from Task 1 (`menu-btn`, `theme-btn`, `drawer`, `drawer-overlay`, `year`), `.open`/`.visible` classes from Task 2, `.copy-btn` style from Task 2.
- Produces: `localStorage` key `theme` with values `light`/`dark`; `data-theme` attribute on `<html>`.

- [x] **Step 1: Write the script**

```xml
<script>
//<![CDATA[
(function () {
  var root = document.documentElement;
  var saved = null;
  try { saved = localStorage.getItem('theme'); } catch (e) {}
  if (saved === 'light' || saved === 'dark') root.setAttribute('data-theme', saved);

  document.getElementById('theme-btn').addEventListener('click', function () {
    var dark = root.getAttribute('data-theme') === 'dark' ||
      (!root.getAttribute('data-theme') && matchMedia('(prefers-color-scheme: dark)').matches);
    var next = dark ? 'light' : 'dark';
    root.setAttribute('data-theme', next);
    try { localStorage.setItem('theme', next); } catch (e) {}
  });

  var drawer = document.getElementById('drawer');
  var overlay = document.getElementById('drawer-overlay');
  var menuBtn = document.getElementById('menu-btn');
  function setDrawer(open) {
    drawer.classList.toggle('open', open);
    overlay.classList.toggle('visible', open);
    menuBtn.setAttribute('aria-expanded', open ? 'true' : 'false');
    if (open) { var f = drawer.querySelector('a, input, button'); if (f) f.focus(); }
    else menuBtn.focus();
  }
  menuBtn.addEventListener('click', function () { setDrawer(!drawer.classList.contains('open')); });
  overlay.addEventListener('click', function () { setDrawer(false); });
  document.addEventListener('keydown', function (e) {
    if (e.key === 'Escape' && drawer.classList.contains('open')) setDrawer(false);
  });

  var y = document.getElementById('year');
  if (y) y.textContent = new Date().getFullYear();

  document.querySelectorAll('.post-body pre').forEach(function (pre) {
    var btn = document.createElement('button');
    btn.className = 'copy-btn'; btn.type = 'button'; btn.textContent = 'Copy';
    btn.addEventListener('click', function () {
      navigator.clipboard.writeText(pre.innerText.replace(/^Copy\n?/, '')).then(function () {
        btn.textContent = 'Copied'; setTimeout(function () { btn.textContent = 'Copy'; }, 1500);
      });
    });
    pre.appendChild(btn);
  });
})();
//]]>
</script>
```

Note: to avoid the copy button's own label being captured by `pre.innerText`, read the text BEFORE appending the button — implement as: capture `var code = pre.innerText;` first, then create/append the button and write `code` in the click handler (drop the `replace` hack).

- [x] **Step 2: Verify well-formedness** — expected `XML OK`.

- [x] **Step 3: Static-year fallback check** — confirm the footer still reads sensibly with JS off (the `#year` span is empty but the copyright line and blog title remain). No change needed if so.

- [ ] **Step 4: Commit** — `feat: add drawer, theme-toggle, copy-button, and year scripts`.

---

### Task 6: Final validation and install docs

**Files:**
- Modify: `README.md` — add an "Installing moderntemplate.xml" section.

**Interfaces:**
- Consumes: finished `moderntemplate.xml`.

- [x] **Step 1: Full-file review** — read `moderntemplate.xml` top to bottom; check every id referenced by JS exists exactly once, every class used in markup exists in the skin, and no `TODO`/placeholder comments remain.

- [x] **Step 2: Well-formedness + size check**

Run: `[xml](Get-Content -Raw moderntemplate.xml) > $null; if ($?) { "XML OK" }; (Get-Item moderntemplate.xml).Length`
Expected: `XML OK` and a size in the 15–40KB range.

- [ ] **Step 3: Add install instructions to README**

Append a section: back up the current theme first (Blogger → Theme → ⋮ → Backup), then Theme → Edit HTML → select-all → paste `moderntemplate.xml` contents → Save; then Layout to arrange drawer widgets; verify homepage, an old post, a code post, label page, search, 404, both color themes, mobile width. Link the Rich Results test (https://search.google.com/test/rich-results) for the JSON-LD.

- [ ] **Step 4: Commit** — `docs: add installation and verification instructions`.

**Manual (user) step after merge:** install on Blogger and run the spec's testing checklist — this cannot be automated locally.
