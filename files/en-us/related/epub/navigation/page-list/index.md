---
title: EPUB page list
short-title: Page list
slug: Related/EPUB/Navigation/Page-list
page-type: guide
sidebar: epub
---

In EPUB 3, a **page list** is an optional navigation list inside the [navigation document](/en-US/docs/Related/EPUB/Navigation) (`nav.xhtml`), marked up with `<nav epub:type="page-list">`:

```xml
<nav epub:type="page-list" hidden="">
  <h2>Page List</h2>
  <ol>
    <li><a href="text/preface.xhtml#page-i">i</a></li>
    <li><a href="text/preface.xhtml#page-ii">ii</a></li>
    <li><a href="text/chapter-1.xhtml#page-1">1</a></li>
    <li><a href="text/chapter-1.xhtml#page-2">2</a></li>
  </ol>
</nav>
```

In a reflowable EPUB, screen pagination shifts whenever a reader changes font size, margins, or device width. A `page-list` links to fixed page-break markers inside the chapters—usually matching a print edition, or static reference pages created for a digital-only edition—so students, book clubs, and academic citations can jump to the exact same passage on any device.

A navigation document may include at most one `<nav epub:type="page-list">` element.

## Structure and content rules

`<nav epub:type="page-list">` follows the [EPUB `<nav>` content rules](/en-US/docs/Related/EPUB/Navigation#content_rules_for_nav_elements):

- `<nav epub:type="page-list">`
  - : Container for the page list (declare `xmlns:epub="http://www.idpf.org/2007/ops"` on `<html>`). Contains an optional heading (`<h1>`–`<h6>`) followed by one `<ol>`. If the page list is rendered visibly in `<spine>` rather than hidden with `hidden=""`, add [`role="doc-pagelist"`](https://www.w3.org/TR/dpub-aria-1.1/#doc-pagelist) and label it with `aria-labelledby` or `aria-label` for screen readers.
- `<ol>`
  - : Should contain a single flat list of `<li>` elements, with no nested `<ol>` sublists or `<span>` group headings.
- `<li>` and `<a>`
  - : Each `<li>` contains an `<a>` link pointing to a page-break anchor inside a content document in [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine) (never an external URL). Set the link text to the bare page number or label (such as `iv`, `1`, `24`, or `A-1` rather than `Page 24`) so reading apps can match it against "Go to page" input.

## Marking page breaks in content documents

Each link in `page-list` points to an anchor inside an [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) chapter where the new page begins. Mark each boundary using `id`, `epub:type="pagebreak"`, and [`role="doc-pagebreak"`](https://www.w3.org/TR/dpub-aria-1.1/#doc-pagebreak):

```xml
<!-- Page break at the start of a chapter or between blocks -->
<div id="page-1" epub:type="pagebreak" role="doc-pagebreak" aria-label="1"></div>
<h1>Chapter 1: Loomings</h1>

<!-- Page break inside a paragraph -->
<p>
  Call me Ishmael. Some years ago—never mind how long precisely—having
  <span id="page-2" epub:type="pagebreak" role="doc-pagebreak" aria-label="2"></span>
  little or no money in my purse, and nothing particular to interest me on shore...
</p>
```

Using an empty `<span>` (inside inline text) or `<div>` (between blocks) with `aria-label` keeps page numbers invisible in reflowed prose while giving reading apps and screen readers the exact location and spoken number of each break. Keep `aria-label` to the page number itself (`aria-label="2"`) so screen readers do not announce "page page 2".

When your EPUB includes static page breaks, [EPUB Accessibility 1.1](https://www.w3.org/TR/epub-a11y-11/#sec-page-nav) requires identifying their source in [`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata) with `<meta property="pageBreakSource">` (using `"none"` if the page breaks were created for a digital-only edition with no print counterpart):

```xml
<meta property="pageBreakSource">urn:isbn:9780142437247</meta>
<meta property="schema:accessibilityFeature">pageNavigation</meta>
```

(`pageBreakSource` replaces the older `<dc:source>` plus `<meta property="source-of">pagination</meta>` pattern.)

## Hiding the page list in `<spine>`

Because a book's `page-list` often has hundreds of links, add [`hidden=""`](/en-US/docs/Web/HTML/Reference/Global_attributes/hidden) to `<nav epub:type="page-list">` whenever `nav.xhtml` is included in `<spine>`. This hides the page list from the visible Contents page while still letting reading apps populate their built-in "Pages" menu or "Go to page" control.

`<nav epub:type="page-list">` replaces the legacy EPUB 2 `<pageList>` structure inside `toc.ncx`.

## Examples

### Navigation document with TOC, landmarks, and page list

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:epub="http://www.idpf.org/2007/ops"
      xml:lang="en"
      lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Contents</title>
    <link rel="stylesheet" type="text/css" href="styles/main.css" />
  </head>
  <body>
    <nav epub:type="toc" role="doc-toc" aria-labelledby="toc-heading" id="toc">
      <h1 id="toc-heading">Contents</h1>
      <ol>
        <li><a href="text/preface.xhtml">Extracts</a></li>
        <li><a href="text/chapter-1.xhtml">Chapter 1: Loomings</a></li>
        <li><a href="text/chapter-2.xhtml">Chapter 2: The Carpet-Bag</a></li>
      </ol>
    </nav>

    <nav epub:type="landmarks" hidden="">
      <h2>Landmarks</h2>
      <ol>
        <li><a epub:type="cover" href="text/cover.xhtml">Cover</a></li>
        <li><a epub:type="toc" href="#toc">Table of Contents</a></li>
        <li><a epub:type="bodymatter" href="text/chapter-1.xhtml">Start of Content</a></li>
      </ol>
    </nav>

    <nav epub:type="page-list" hidden="">
      <h2>Page List</h2>
      <ol>
        <li><a href="text/preface.xhtml#page-i">i</a></li>
        <li><a href="text/preface.xhtml#page-ii">ii</a></li>
        <li><a href="text/chapter-1.xhtml#page-1">1</a></li>
        <li><a href="text/chapter-1.xhtml#page-2">2</a></li>
        <li><a href="text/chapter-2.xhtml#page-3">3</a></li>
      </ol>
    </nav>
  </body>
</html>
```

## Specifications

- [EPUB 3.3: The `page-list nav` element](https://www.w3.org/TR/epub-33/#sec-nav-pagelist)
- [EPUB 3.3: The `nav` element: restrictions](https://www.w3.org/TR/epub-33/#sec-nav-def-model)
- [EPUB 3.3: Using in the spine](https://www.w3.org/TR/epub-33/#sec-nav-doc-use-spine)
- [EPUB Accessibility 1.1: Page navigation](https://www.w3.org/TR/epub-a11y-11/#sec-page-nav)
- [Digital Publishing WAI-ARIA Module 1.1: `doc-pagelist`](https://www.w3.org/TR/dpub-aria-1.1/#doc-pagelist)

## See also

- [EPUB navigation document](/en-US/docs/Related/EPUB/Navigation)
- [EPUB table of contents (TOC)](/en-US/docs/Related/EPUB/Navigation/TOC)
- [EPUB landmarks](/en-US/docs/Related/EPUB/Navigation/Landmarks)
- [EPUB package metadata](/en-US/docs/Related/EPUB/Package/Metadata)
- [EPUB package spine](/en-US/docs/Related/EPUB/Package/Spine)
- [`<nav>` HTML element](/en-US/docs/Web/HTML/Reference/Elements/nav)
