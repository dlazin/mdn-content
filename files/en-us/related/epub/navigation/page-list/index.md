---
title: EPUB page list
short-title: Page list
slug: Related/EPUB/Navigation/Page-list
page-type: guide
sidebar: epub
---

In EPUB 3, a **page list** is an optional navigation list inside the publication's [navigation document](/en-US/docs/Related/EPUB/Navigation) (commonly named `nav.xhtml`), defined by an HTML [`<nav>`](/en-US/docs/Web/HTML/Reference/Elements/nav) element with the `epub:type="page-list"` attribute:

```xml
<nav epub:type="page-list" role="doc-pagelist" aria-labelledby="pagelist-heading" hidden="">
  <h2 id="pagelist-heading">Page List</h2>
  <ol>
    <li><a href="text/preface.xhtml#page-i">i</a></li>
    <li><a href="text/preface.xhtml#page-ii">ii</a></li>
    <li><a href="text/chapter-1.xhtml#page-1">1</a></li>
    <li><a href="text/chapter-1.xhtml#page-2">2</a></li>
  </ol>
</nav>
```

In a reflowable EPUB, screen pagination changes dynamically whenever a reader adjusts the font size, margins, or viewport width, meaning two people reading the same ebook on different devices will not see the same screen page numbers. A `page-list` solves this by linking to static page-break markers embedded in the content documents—typically matching the page numbers of a print edition, or static reference pages defined for a digital-only edition. This lets students in a classroom, book club members, or academic citations jump to the exact same passage regardless of device settings or assistive technology.

An EPUB [navigation document](/en-US/docs/Related/EPUB/Navigation) may include **at most one** `<nav epub:type="page-list">` element.

## Structure and content rules

Because `<nav epub:type="page-list">` carries an `epub:type` attribute inside an EPUB navigation document, it follows the restricted EPUB `<nav>` content model along with several `page-list`-specific conventions:

- `<nav epub:type="page-list">`
  - : The container element for the page list. At most one `<nav epub:type="page-list">` element may appear in the navigation document, and an ancestor element (typically the root `<html>` element) must declare the EPUB namespace: `xmlns:epub="http://www.idpf.org/2007/ops"`. Including `role="doc-pagelist"` (from the [Digital Publishing WAI-ARIA Module](https://www.w3.org/TR/dpub-aria-1.1/#doc-pagelist)) is also recommended to expose the page list role to assistive technologies.

    Inside `<nav epub:type="page-list">`, only two direct child elements are permitted, in order:
    1. An optional heading element ([`<h1>`–`<h6>`](/en-US/docs/Web/HTML/Reference/Elements/Heading_Elements)).
    2. **Exactly one** ordered list ([`<ol>`](/en-US/docs/Web/HTML/Reference/Elements/ol)). Unordered lists ([`<ul>`](/en-US/docs/Web/HTML/Reference/Elements/ul)) are not allowed.

- [`<h1>`–`<h6>`](/en-US/docs/Web/HTML/Reference/Elements/Heading_Elements) (optional)
  - : A single heading that titles the page list (for example, `<h2 id="pagelist-heading">Page List</h2>`). Pair the heading's `id` with `aria-labelledby` on `<nav>` (or set `aria-label` directly on `<nav>`) so assistive technologies announce an accessible name for the navigation landmark.
- [`<ol>`](/en-US/docs/Web/HTML/Reference/Elements/ol)
  - : Contains one or more [`<li>`](/en-US/docs/Web/HTML/Reference/Elements/li) elements in publication order. Like [`landmarks`](/en-US/docs/Related/EPUB/Navigation/Landmarks), a `page-list` should be **flat**—containing only a single `<ol>` with no nested `<ol>` sublists (and therefore no unlinked `<span>` group headings). Default list styling inside `<nav>` elements is equivalent to `list-style: none`.
- [`<li>`](/en-US/docs/Web/HTML/Reference/Elements/li) and [`<a>`](/en-US/docs/Web/HTML/Reference/Elements/a)
  - : Each `<li>` contains an `<a>` element whose `href` resolves to a page-break target inside a content document in the EPUB container (such as `text/chapter-1.xhtml#page-24`), never to an external `http:` or `https:` URL. Any content document referenced in `page-list` must also be listed in the package [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).

    Set the text content of each `<a>` element to the bare page number or label itself (for example, `iv`, `1`, `24`, or `A-1` rather than `Page 24`), as reading systems display these labels in compact page-list menus and match them against user "Go to page" input.

## Marking page breaks in content documents

Each link in `<nav epub:type="page-list">` points to a corresponding page-break anchor inside an [XHTML](/en-US/docs/Glossary/XHTML) content document. Mark each static page boundary at the exact point where the new page starts using an `id` attribute paired with `epub:type="pagebreak"` and the ARIA [`role="doc-pagebreak"`](https://www.w3.org/TR/dpub-aria-1.1/#doc-pagebreak) attribute:

```xml
<!-- Page break occurring at the start of a chapter or between paragraphs -->
<div id="page-1" epub:type="pagebreak" role="doc-pagebreak" aria-label="1"></div>
<h1>Chapter 1: Loomings</h1>

<!-- Page break occurring mid-paragraph -->
<p>
  Call me Ishmael. Some years ago—never mind how long precisely—having
  <span id="page-2" epub:type="pagebreak" role="doc-pagebreak" aria-label="2"></span>
  little or no money in my purse, and nothing particular to interest me on shore…
</p>
```

Using an empty `<span>` (inside inline text) or `<div>` (between block elements) with `aria-label` keeps the print page number invisible in normal reflowed prose while giving both `<nav epub:type="page-list">` and screen readers the exact location and spoken label of each page break.

> [!NOTE]
> When your EPUB includes a `page-list` and page-break markers derived from a print or statically paginated edition, [EPUB Accessibility 1.1](https://www.w3.org/TR/epub-a11y-11/#sec-page-nav) requires identifying that source edition in the package [`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata) (for example, `<dc:source>urn:isbn:9780142437247</dc:source>`).

## Hiding the page list in the reading order

Because a full-length book's `page-list` can contain hundreds of page links, you almost never want it rendered visibly on the in-book table of contents page when `nav.xhtml` is included in the package [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).

Add the HTML [`hidden`](/en-US/docs/Web/HTML/Reference/Global_attributes/hidden) attribute to `<nav epub:type="page-list">` to exclude it from rendering in the `<spine>` content flow:

```xml
<nav epub:type="page-list" role="doc-pagelist" aria-labelledby="pagelist-heading" hidden="">
```

The `hidden` attribute only suppresses rendering when `nav.xhtml` is viewed as a page in the `<spine>`—reading systems still read `<nav epub:type="page-list">` and expose its page navigation in their built-in UI controls (such as a "Pages" tab or "Go to page" dialog). Because `nav.xhtml` is an XML document (`application/xhtml+xml`), always write `hidden=""` or `hidden="hidden"` rather than the bare HTML boolean shorthand `hidden`.

## Legacy NCX `<pageList>`

In EPUB 2, static page lists were stored inside the `.ncx` file (`toc.ncx`) using `<pageList>` and `<pageTarget>` elements.

In EPUB 3, `<nav epub:type="page-list">` replaces the NCX `<pageList>`. Including an NCX file is optional and is only needed if you want to support legacy EPUB 2 reading systems.

## Examples

### Navigation document with TOC, landmarks, and page list

The following example shows a complete `nav.xhtml` file containing the required `<nav epub:type="toc">` element alongside hidden `landmarks` and `page-list` navigation elements:

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

    <nav epub:type="page-list" role="doc-pagelist" aria-labelledby="pagelist-heading" hidden="">
      <h2 id="pagelist-heading">Page List</h2>
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

- [EPUB navigation](/en-US/docs/Related/EPUB/Navigation)
- [EPUB table of contents (TOC)](/en-US/docs/Related/EPUB/Navigation/TOC)
- [EPUB landmarks](/en-US/docs/Related/EPUB/Navigation/Landmarks)
- [EPUB package metadata](/en-US/docs/Related/EPUB/Package/Metadata)
- [EPUB package spine](/en-US/docs/Related/EPUB/Package/Spine)
- [`<nav>`: The HTML Navigation Section element](/en-US/docs/Web/HTML/Reference/Elements/nav)
