# Basic Blogger Template

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![Documentation Status](https://readthedocs.org/projects/basic-blogger-template/badge/?version=latest)](http://basic-blogger-template.readthedocs.io/en/latest/?badge=latest) 

This template is built from scratch with schema markup,  i use schema because this help search engine understand the part of the website structure and help indexing in search engine. And i use open graph protocol for help social media i.e facebook to scraping and make preview for blog posting.

### Installing moderntemplate.xml

`moderntemplate.xml` is the modern v3 theme (warm editorial design, dark mode, slide-out drawer, styled code blocks). To install:

1. **Back up your current theme first**: Blogger dashboard → Theme → ⋮ → Backup.
2. Theme → Customize dropdown → **Edit HTML** → select all → paste the contents of `moderntemplate.xml` → Save.
3. Go to **Layout** to arrange the drawer widgets (Search, About, Labels, Archive).
4. Verify: homepage, an old post, a code-heavy post, a label page, search, a bad URL (404), both light/dark themes, and a phone-width window.
5. Check post schema with the [Rich Results test](https://search.google.com/test/rich-results).

Code blocks: paste code inside `<pre><code>...</code></pre>` in HTML view; a Copy button is added automatically. Optional manual syntax highlighting via `<span class='tok-k'>` (keyword), `tok-s` (string), `tok-c` (comment), `tok-n` (number), `tok-f` (function).

### Getting Started

Please review the markup structure before editing and make any changes using this template.

~~~
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE html>
<html b:version='2' class='v2' expr:dir='data:blog.languageDirection' xmlns='http://www.w3.org/1999/xhtml' xmlns:b='http://www.google.com/2005/gml/b' xmlns:data='http://www.google.com/2005/gml/data' xmlns:expr='http://www.google.com/2005/gml/expr' xmlns:og='http://ogp.me/ns#'>
<head>
<meta charset='UTF-8'/>
<title><data:blog.title/></title>
</head>
<body>
<div id='wrapper'>
<header id='header-wrapper'>
<b:section class='header' id='header' maxwidgets='1'>
<b:widget id='Header1' locked='true' title='Basic Blogger Template (Header)' type='Header'></b:widget>
</b:section>
</header>
<nav id='navigation'>
<ul>
<li><a href=''>Home</a></li>
<li><a href=''>About</a></li>
</ul>
</nav>
<div class='clearfix'/>
<section id='outer-wrapper'>
<article id='article-wrapper'>
<b:section class='main' id='main'>
<b:widget id='Blog1' locked='true' title='Blog Posting' type='Blog'></b:widget>
</b:section>
</article>
</section>
<div class='clearfix'/>
<aside id='sidebar-wrapper'>
<b:section class='sidebar' id='sidebar' showaddelement='yes'></b:section>
</aside>
<div class='clearfix'/>
<footer id='footer-wrapper'>
<b:section class='footer' id='footer' showaddelement='yes'></b:section>
</footer>
</div>
</body>
</html>
~~~

### Debugging

* [Structured Data Testing Tool](https://search.google.com/structured-data/testing-tool?hl=id)
* [Open Graph Debugger](https://developers.facebook.com/tools/debug/)

### Technologies

* [Open Graph Protocol](http://ogp.me/)
* [Schema](http://schema.org/)


### Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct, and the process for submitting pull requests to us.

### Prerequisite

* HTML & CSS
* XML
* Javascript & Jquery (optional, if you need your Blog more interractive you need a Javascript, Jquery).

### Authors

* Agus Purwantoro *initial work* - [meagusp](https://github.com/meagusp)

### License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.
