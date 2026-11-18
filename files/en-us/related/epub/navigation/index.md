---
title: EPUB navigation document
short-title: Overview
slug: Related/EPUB/Navigation
page-type: guide
sidebar: epub
---

Every EPUB 3 book includes one **navigation document** (commonly named `nav.xhtml` or `toc.xhtml`). It is an [XHTML content document](/en-US/docs/Related/EPUB/Content/XHTML) (`application/xhtml+xml`) that powers the reading system's built-in navigation menus and can also appear as a table of contents page inside the book.

Identify the navigation document in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) by setting `properties="nav"` on its `<item>`:

```xml
<manifest>
  <item id="nav" href="nav.xhtml" media-type="application/xhtml+xml" properties="nav" />
  <!-- other manifest items -->
</manifest>
```

Inside `nav.xhtml`, each navigation list uses an HTML [`<nav>`](/en-US/docs/Web/HTML/Reference/Elements/nav) element with an [`epub:type`](/en-US/docs/Related/EPUB/Content/epub:type) attribute:

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
        <li><a href="text/preface.xhtml">Preface</a></li>
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
        <li><a href="text/chapter-1.xhtml#page-1">1</a></li>
        <li><a href="text/chapter-2.xhtml#page-2">2</a></li>
      </ol>
    </nav>
  </body>
</html>
```

## Types of `<nav>` elements

EPUB 3 defines three standard `<nav epub:type="...">` blocks—each allowed at most once in `nav.xhtml`—plus optional custom lists:

- [Table of contents (`epub:type="toc"`)](/en-US/docs/Related/EPUB/Navigation/TOC)
  - : Required. The book's primary outline of chapters and sections.
- [Landmarks (`epub:type="landmarks"`)](/en-US/docs/Related/EPUB/Navigation/Landmarks)
  - : Optional. A short, flat list of major structural entry points (such as `cover`, `toc`, `bodymatter`, `glossary`, or `index`), where each `<a>` link carries its own `epub:type` attribute.
- [Page list (`epub:type="page-list"`)](/en-US/docs/Related/EPUB/Navigation/Page-list)
  - : Optional. A flat list mapping static page numbers (usually from a print edition) to page-break anchors in the chapters.
- Custom `<nav>` lists (such as `loi` or `lot`)
  - : Optional. Additional `<nav epub:type="...">` blocks for lists such as illustrations (`loi`) or tables (`lot`):

    ```xml
    <nav epub:type="loi" aria-labelledby="loi-heading">
      <h2 id="loi-heading">Illustrations</h2>
      <ol>
        <li><a href="text/chapter-1.xhtml#fig-1">Map of Nantucket</a></li>
        <li><a href="text/chapter-2.xhtml#fig-2">The Whaling Ship Pequod</a></li>
      </ol>
    </nav>
    ```

    Most reading apps do not build separate UI tabs for custom `<nav>` lists, so readers see them only if you include `nav.xhtml` in `<spine>` without hiding those lists.

## Content rules for `<nav>` elements

Outside `<nav epub:type="...">` elements, `nav.xhtml` can contain any valid [EPUB XHTML](/en-US/docs/Related/EPUB/Content/XHTML) markup. Inside any `<nav>` element with an `epub:type` attribute, EPUB restricts the HTML structure so reading apps can parse menus reliably:

- Declare `xmlns:epub="http://www.idpf.org/2007/ops"` on `<html>` (or another ancestor).
- Inside `<nav epub:type="...">`, include an optional heading (`<h1>`–`<h6>`) followed by a single [`<ol>`](/en-US/docs/Web/HTML/Reference/Elements/ol) ordered list. Unordered lists (`<ul>`), `<p>` paragraphs, and `<div>` wrappers are not allowed.
- Each `<li>` inside `<ol>` must start with either an [`<a>`](/en-US/docs/Web/HTML/Reference/Elements/a) link (optionally followed by one nested `<ol>`) or an unlinked [`<span>`](/en-US/docs/Web/HTML/Reference/Elements/span) group heading (which must be followed by one nested `<ol>`).
- Every `<a>` and `<span>` must produce a non-empty text label. Because reading apps strip HTML styling, images, and MathML when building native menus, make sure every entry reads clearly as plain text (and include `alt` text on any images inside `<a>`).
- Links inside `toc`, `landmarks`, and `page-list` must point to content documents in the EPUB archive that are also listed in [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine). Only custom `<nav>` types may link to external URLs.
- List items inside EPUB `<nav>` elements default to `list-style: none`, so `<ol>` entries do not display automatic numbers (`1.`, `2.`, `3.`) unless you add custom CSS.

## Including `nav.xhtml` in the reading order

Reading systems extract their navigation menus from `<item properties="nav">` whether or not `nav.xhtml` is listed in [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).

If you omit `nav.xhtml` from `<spine>`, readers open the table of contents only through the app's toolbar or menu. If you reference `nav.xhtml` in `<spine>`, it also renders as a styled page inside the book, avoiding a second HTML table of contents that could drift out of sync.

When `nav.xhtml` is in `<spine>`, add the HTML [`hidden`](/en-US/docs/Web/HTML/Reference/Global_attributes/hidden) attribute (`hidden=""` in XML syntax) to `<nav>` blocks or deep `<ol>` sublists that you do not want shown on the in-book page (typically `landmarks` and `page-list`). Reading systems still parse `hidden=""` navigation elements for their built-in menus.

## EPUB 2 navigation formats

EPUB 3's `nav.xhtml` replaces both EPUB 2 navigation structures: the standalone XML `.ncx` file (`toc.ncx`, referenced by `<spine toc="...">`) and the `<guide>` element in `package.opf`. You only need to include `toc.ncx` or `<guide>` if you must support older EPUB 2 e-readers.

## Specifications

- [EPUB 3.3: EPUB navigation document](https://www.w3.org/TR/epub-33/#sec-nav)
- [EPUB 3.3: Navigation document requirements](https://www.w3.org/TR/epub-33/#sec-nav-content-req)
- [EPUB 3.3: The `nav` element: restrictions](https://www.w3.org/TR/epub-33/#sec-nav-def-model)
- [EPUB 3.3: The `nav` element: types](https://www.w3.org/TR/epub-33/#sec-nav-def-types)
- [EPUB 3.3: Using in the spine](https://www.w3.org/TR/epub-33/#sec-nav-doc-use-spine)

## See also

- [EPUB table of contents (TOC)](/en-US/docs/Related/EPUB/Navigation/TOC)
- [EPUB landmarks](/en-US/docs/Related/EPUB/Navigation/Landmarks)
- [EPUB page list](/en-US/docs/Related/EPUB/Navigation/Page-list)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
- [EPUB package spine](/en-US/docs/Related/EPUB/Package/Spine)
