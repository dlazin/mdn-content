---
title: EPUB page list
short-title: Page list
slug: Related/EPUB/Reference/Navigation/Page-list
page-type: guide
sidebar: epub
---

In EPUB 3, a **page list** is an optional navigation list inside the [navigation document](/en-US/docs/Related/EPUB/Reference/Navigation), marked up with `<nav epub:type="page-list">`:

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

Because reflowable pagination shifts whenever a reader changes font size, margins, or screen width, a page list links to fixed page-break markers inside the chapters—usually matching a print edition, or static reference pages created for a digital-only book—so students, book clubs, and citations can jump to the exact same passage on any device. A navigation document may contain at most one page list.

## Page list rules

The page list follows the [EPUB `<nav>` content rules](/en-US/docs/Related/EPUB/Reference/Navigation#content_rules_for_nav_elements) with these specifics:

- Keep `<ol>` as a single flat list of `<li>` elements without nested `<ol>` sublists or `<span>` group headings.
- Set each `<a>` link's text to the bare page number or label (such as `iv`, `1`, `24`, or `A-1` rather than `Page 24`) so reading apps can match it against "Go to page" input.
- Add [`hidden=""`](/en-US/docs/Web/HTML/Reference/Global_attributes/hidden) to the `<nav>` element when the navigation document is [included in the spine](/en-US/docs/Related/EPUB/Reference/Navigation#including_the_navigation_document_in_the_reading_order) so hundreds of page links do not clutter the visible Contents page. If you do render the page list visibly without `hidden=""`, add [`role="doc-pagelist"`](https://www.w3.org/TR/dpub-aria-1.1/#doc-pagelist) and label `<nav>` with `aria-labelledby` or `aria-label`.

## Marking page breaks in content documents

Each link in the page list points to an anchor inside an [XHTML](/en-US/docs/Related/EPUB/Reference/Content/XHTML) chapter where the new page begins. Mark each boundary using `id`, `epub:type="pagebreak"`, and [`role="doc-pagebreak"`](https://www.w3.org/TR/dpub-aria-1.1/#doc-pagebreak):

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

When your EPUB includes static page breaks, [EPUB Accessibility 1.1](https://www.w3.org/TR/epub-a11y-11/#sec-page-nav) requires identifying their source in the [package metadata](/en-US/docs/Related/EPUB/Reference/Package/Metadata) with `<meta property="pageBreakSource">` (using `"none"` if the page breaks were created for a digital-only edition with no print counterpart):

```xml
<meta property="pageBreakSource">urn:isbn:9780142437247</meta>
<meta property="schema:accessibilityFeature">pageNavigation</meta>
```

(`pageBreakSource` replaces the older `<dc:source>` plus `<meta property="source-of">pagination</meta>` pattern.)

## Specifications

- [EPUB 3.3: The `page-list nav` element](https://www.w3.org/TR/epub-33/#sec-nav-pagelist)
- [EPUB 3.3: The `nav` element: restrictions](https://www.w3.org/TR/epub-33/#sec-nav-def-model)
- [EPUB Accessibility 1.1: Page navigation](https://www.w3.org/TR/epub-a11y-11/#sec-page-nav)
- [Digital Publishing WAI-ARIA Module 1.1: `doc-pagelist`](https://www.w3.org/TR/dpub-aria-1.1/#doc-pagelist)

## See also

- [EPUB navigation document](/en-US/docs/Related/EPUB/Reference/Navigation)
- [EPUB table of contents (TOC)](/en-US/docs/Related/EPUB/Reference/Navigation/TOC)
- [EPUB landmarks](/en-US/docs/Related/EPUB/Reference/Navigation/Landmarks)
- [EPUB package metadata](/en-US/docs/Related/EPUB/Reference/Package/Metadata)
