---
title: EPUB landmarks
short-title: Landmarks
slug: Related/EPUB/Navigation/Landmarks
page-type: guide
sidebar: epub
---

In EPUB 3, **landmarks** are an optional navigation list inside the [navigation document](/en-US/docs/Related/EPUB/Navigation) (`nav.xhtml`), marked up with `<nav epub:type="landmarks">`:

```xml
<nav epub:type="landmarks" hidden="">
  <h2>Landmarks</h2>
  <ol>
    <li><a epub:type="cover" href="text/cover.xhtml">Cover</a></li>
    <li><a epub:type="toc" href="#toc">Table of Contents</a></li>
    <li><a epub:type="bodymatter" href="text/chapter-1.xhtml">Start of Content</a></li>
  </ol>
</nav>
```

Landmarks identify a small set of structural entry points—such as the cover, table of contents, start of main text (`bodymatter`), glossary, or index—so reading apps can offer quick-jump shortcuts or open the book directly at Chapter 1 on first launch. A navigation document may contain at most one `<nav epub:type="landmarks">` element.

## Landmarks vs. the table of contents

While the [table of contents](/en-US/docs/Related/EPUB/Navigation/TOC) (`epub:type="toc"`) is a full, multi-level chapter outline for the reader, `landmarks` is a short, flat list of structural roles for the reading system. Every `<a>` link in `landmarks` carries an `epub:type` attribute identifying what the target section represents. Do not repeat every chapter in `landmarks`.

## Structure and content rules

`<nav epub:type="landmarks">` follows the [EPUB `<nav>` content rules](/en-US/docs/Related/EPUB/Navigation#content_rules_for_nav_elements) with additional rules for landmark links:

- `<nav epub:type="landmarks">`
  - : Container for landmarks (declare `xmlns:epub="http://www.idpf.org/2007/ops"` on `<html>`). Contains an optional heading (`<h1>`–`<h6>`) followed by one `<ol>`. Unlike `toc` and `page-list`, `landmarks` has no specialized DPUB-ARIA `doc-*` role; if rendered visibly without `hidden=""`, label `<nav>` with `aria-labelledby` or `aria-label` so screen readers can distinguish it from the table of contents `<nav>`.
- `<ol>`
  - : Should contain a single flat list of `<li>` elements, with no nested `<ol>` sublists or `<span>` headings.
- `<li>` and `<a>`
  - : Each `<li>` contains an `<a>` link that:
    - Includes a required `epub:type` attribute (usually from the [EPUB 3 Structural Semantics Vocabulary](https://www.w3.org/TR/epub-ssv-11/)).
    - Points to a content document (or `#` fragment) inside the EPUB that is also listed in [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine), never an external URL.
    - Uses a clear, human-readable text label, since some reading apps show landmark links directly in a menu.
    - Does not duplicate another landmark entry with both the same `epub:type` value and the same target URL.

## Common landmark `epub:type` values

Keep `landmarks` short and focused on major sections:

- `bodymatter`
  - : Start of the main text (usually Chapter 1, after the frontmatter). Platforms such as Apple Books and Amazon Kindle (during EPUB conversion) use `bodymatter` to set the initial opening page when a reader opens the book for the first time.
- `toc`
  - : The in-book [table of contents](/en-US/docs/Related/EPUB/Navigation/TOC) when `nav.xhtml` (or another TOC file) is included in [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).
- `cover`
  - : The XHTML or SVG cover page in `<spine>` (not a raw `.jpg` or `.png` image file).
- `glossary`, `index`, `bibliography`
  - : Reference sections in the backmatter.
- `loi`, `lot`
  - : List of illustrations (`loi`) or list of tables (`lot`).
- `frontmatter`, `preface`, `introduction`, `acknowledgments`, `copyright-page`
  - : Key frontmatter sections.

## Hiding landmarks in `<spine>`

When `nav.xhtml` is included in `<spine>` as a visible Contents page, add [`hidden=""`](/en-US/docs/Web/HTML/Reference/Global_attributes/hidden) to `<nav epub:type="landmarks">` so the landmarks list does not appear on that page. Reading systems still read `hidden=""` landmarks for their built-in UI.

`<nav epub:type="landmarks">` replaces the legacy EPUB 2 `<guide>` element in `package.opf`, which EPUB 3 reading systems ignore.

## Examples

### Navigation document with TOC and hidden landmarks

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
        <li><a href="text/illustrations.xhtml">Illustrations</a></li>
        <li><a href="text/glossary.xhtml">Nautical Glossary</a></li>
      </ol>
    </nav>

    <nav epub:type="landmarks" hidden="">
      <h2>Landmarks</h2>
      <ol>
        <li><a epub:type="cover" href="text/cover.xhtml">Cover</a></li>
        <li><a epub:type="toc" href="#toc">Table of Contents</a></li>
        <li><a epub:type="bodymatter" href="text/chapter-1.xhtml">Start of Content</a></li>
        <li><a epub:type="loi" href="text/illustrations.xhtml">List of Illustrations</a></li>
        <li><a epub:type="glossary" href="text/glossary.xhtml">Glossary</a></li>
      </ol>
    </nav>
  </body>
</html>
```

## Specifications

- [EPUB 3.3: The `landmarks nav` element](https://www.w3.org/TR/epub-33/#sec-nav-landmarks)
- [EPUB 3.3: The `nav` element: restrictions](https://www.w3.org/TR/epub-33/#sec-nav-def-model)
- [EPUB 3.3: Using in the spine](https://www.w3.org/TR/epub-33/#sec-nav-doc-use-spine)
- [EPUB 3 Structural Semantics Vocabulary 1.1](https://www.w3.org/TR/epub-ssv-11/)

## See also

- [EPUB navigation document](/en-US/docs/Related/EPUB/Navigation)
- [EPUB table of contents (TOC)](/en-US/docs/Related/EPUB/Navigation/TOC)
- [EPUB page list](/en-US/docs/Related/EPUB/Navigation/Page-list)
- [EPUB package spine](/en-US/docs/Related/EPUB/Package/Spine)
- [`<nav>` HTML element](/en-US/docs/Web/HTML/Reference/Elements/nav)
