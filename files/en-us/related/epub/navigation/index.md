---
title: EPUB navigation document
short-title: Overview
slug: Related/EPUB/Navigation
page-type: guide
sidebar: epub
---

Every EPUB 3 publication must include exactly one **navigation document** (commonly named `nav.xhtml` or `toc.xhtml`). The navigation document is a specialized [XHTML content document](/en-US/docs/Related/EPUB/Content/XHTML) (`application/xhtml+xml`) that provides both a machine-readable navigation layer for the reading system's menus and, optionally, a human-readable table of contents page inside the book.

You register the navigation document in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) by adding `properties="nav"` to its `<item>` entry:

```xml
<manifest>
  <item id="nav" href="nav.xhtml" media-type="application/xhtml+xml" properties="nav" />
  <!-- other manifest items -->
</manifest>
```

Inside `nav.xhtml`, each navigation list is marked up using an HTML [`<nav>`](/en-US/docs/Web/HTML/Reference/Elements/nav) element with an [`epub:type`](/en-US/docs/Related/EPUB/Content/epub:type) attribute:

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
  </body>
</html>
```

## Types of `<nav>` elements

EPUB 3 defines three standard `<nav epub:type="...">` structures—each of which may appear at most once in `nav.xhtml`—plus support for optional custom lists:

- **[Table of contents (`epub:type="toc"`)](/en-US/docs/Related/EPUB/Navigation/TOC)** _(required, exactly one)_
  - : Defines the publication's primary hierarchical outline of chapters and sections. Every EPUB navigation document must contain `<nav epub:type="toc">`.
- **[Landmarks (`epub:type="landmarks"`)](/en-US/docs/Related/EPUB/Navigation/Landmarks)** _(optional, at most one)_
  - : A short, flat list of major structural entry points—such as `cover`, `toc`, `bodymatter` (start of main text), `glossary`, or `index`—where each `<a>` link carries its own `epub:type` attribute.
- **[Page list (`epub:type="page-list"`)](/en-US/docs/Related/EPUB/Navigation/Page-list)** _(optional, at most one)_
  - : A flat list mapping static page numbers (typically matching a print edition) to `epub:type="pagebreak"` anchors inside content documents so readers can jump to exact page citations regardless of font size.
- **Custom `<nav>` lists (such as `loi` or `lot`)** _(optional, zero or more)_
  - : You can include additional `<nav epub:type="...">` blocks for specialized lists—such as a List of Illustrations (`epub:type="loi"`), List of Tables (`epub:type="lot"`), or List of Audio Clips (`epub:type="loa"`):

    ```xml
    <nav epub:type="loi" aria-labelledby="loi-heading">
      <h2 id="loi-heading">Illustrations</h2>
      <ol>
        <li><a href="text/chapter-1.xhtml#fig-1">Map of Nantucket</a></li>
        <li><a href="text/chapter-2.xhtml#fig-2">The Whaling Ship Pequod</a></li>
      </ol>
    </nav>
    ```

    Note that most reading systems do not create dedicated UI menu tabs for custom `<nav>` types; readers typically access custom lists only if you include `nav.xhtml` in the package [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine) without hiding those `<nav>` elements.

## Shared `<nav>` content model rules

Outside `<nav epub:type="...">` elements, `nav.xhtml` can contain any valid [EPUB XHTML](/en-US/docs/Related/EPUB/Content/XHTML) markup. Inside **any** `<nav>` element that carries an `epub:type` attribute, however, EPUB enforces a strict HTML structure so reading systems can parse navigation menus reliably:

1. **EPUB XML namespace**: An ancestor element (typically `<html xmlns:epub="http://www.idpf.org/2007/ops">`) must declare the `epub` namespace prefix.
2. **Heading and single `<ol>`**: Each `<nav epub:type="...">` may contain an optional single heading (`<h1>`–`<h6>`), followed by **exactly one** ordered list ([`<ol>`](/en-US/docs/Web/HTML/Reference/Elements/ol)). Unordered lists (`<ul>`), `<p>` paragraphs, and `<div>` wrappers are not permitted inside `<nav epub:type="...">`.
3. **List items (`<li>`)**: Every `<ol>` must contain one or more `<li>` elements. Each `<li>` must start with either:
   - An [`<a>`](/en-US/docs/Web/HTML/Reference/Elements/a) link, optionally followed by one nested `<ol>` for subsections (in hierarchical lists such as `toc` or custom lists), or
   - An unlinked [`<span>`](/en-US/docs/Web/HTML/Reference/Elements/span) group heading, which **must** be followed by one nested `<ol>`.
4. **Non-empty text labels**: Every `<a>` and `<span>` must produce a non-empty text label (from its text content or via `alt`/`title` attributes on embedded elements). Because app-based reading systems build their own native TOC menus and strip inline HTML styling, MathML, and images, make sure every entry reads clearly as plain text.
5. **Link targets**: Every `<a>` inside `toc`, `landmarks`, and `page-list` must resolve to a content document inside the EPUB container that is also listed in the package [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine) (only custom `<nav>` types are permitted to link to external web URLs).
6. **Default list styling**: By default, list items inside EPUB `<nav>` elements render as `list-style: none` so `<ol>` entries do not automatically display browser-generated numbers (`1.`, `2.`, `3.`). You can apply custom CSS when rendering `nav.xhtml` in the `<spine>`.

## Including `nav.xhtml` in the `<spine>`

Reading systems always extract their built-in navigation menus from the `<item properties="nav">` file in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest), whether or not `nav.xhtml` appears in the [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine):

- **Out of `<spine>`**: Readers access the table of contents exclusively through the e-reader app's toolbar or menu button; they do not turn past an in-book TOC page while reading.
- **In `<spine>`**: Referencing `<itemref idref="nav" />` in `<spine>` displays `nav.xhtml` as an actual page inside the book (styled with your CSS) while using the same file to power the e-reader's TOC menu—avoiding two separate tables of contents that could drift out of sync.

When you include `nav.xhtml` in `<spine>`, add the HTML [`hidden`](/en-US/docs/Web/HTML/Reference/Global_attributes/hidden) attribute (`hidden=""` or `hidden="hidden"` in XML syntax) to `<nav>` elements or deep `<ol>` sub-branches that you want to hide from the visible in-book page (most commonly `landmarks` and `page-list`). Reading systems still read `hidden=""` navigation structures for their built-in UI menus.

## Relationship to EPUB 2 navigation

In EPUB 2, machine-readable navigation was split across two XML structures:

- A standalone NCX file (`toc.ncx`, `application/x-dtbncx+xml`) referenced by `<spine toc="...">` for the table of contents and page list.
- A `<guide>` element at the bottom of `package.opf` for structural landmarks.

In EPUB 3, `nav.xhtml` replaces both `toc.ncx` and `<guide>` with standard HTML markup that screen readers and browser engines already understand. Including `toc.ncx` or `<guide>` in an EPUB 3 file is optional and only necessary if you must support legacy EPUB 2 devices.

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
