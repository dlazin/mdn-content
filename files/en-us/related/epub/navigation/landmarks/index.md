---
title: EPUB landmarks
short-title: Landmarks
slug: Related/EPUB/Navigation/Landmarks
page-type: guide
sidebar: epub
---

In EPUB 3, **landmarks** are an optional navigation list inside the publication's [navigation document](/en-US/docs/Related/EPUB/Navigation) (commonly named `nav.xhtml`), defined by an HTML [`<nav>`](/en-US/docs/Web/HTML/Reference/Elements/nav) element with the `epub:type="landmarks"` attribute:

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

Landmarks identify a small set of major structural points of interest in the ebook—such as the cover page, the table of contents, the start of the main text (`bodymatter`), the glossary, or the index—so reading systems can provide quick-jump shortcuts in their UI or automatically open the book at the start of the main content on first launch.

An EPUB [navigation document](/en-US/docs/Related/EPUB/Navigation) may include **at most one** `<nav epub:type="landmarks">` element. Unlike the [`<nav epub:type="toc">`](/en-US/docs/Related/EPUB/Navigation/TOC) table of contents, `<nav epub:type="landmarks">` is optional, though including at least a `bodymatter` landmark is widely recommended.

## Landmarks vs. the table of contents

Although both live inside the navigation document, `<nav epub:type="landmarks">` and [`<nav epub:type="toc">`](/en-US/docs/Related/EPUB/Navigation/TOC) serve different purposes:

- **The [table of contents](/en-US/docs/Related/EPUB/Navigation/TOC) (`epub:type="toc"`)** is a complete, potentially multi-level outline of chapters and sections written for the human reader.
- **Landmarks (`epub:type="landmarks"`)** are a short, flat list of machine-recognizable structural entry points. Every `<a>` link in `landmarks` must carry its own `epub:type` attribute so the reading system knows the semantic role of the target—not just its display text. Do not repeat every chapter from the table of contents in `landmarks`.

## Structure and content rules

Because `<nav epub:type="landmarks">` carries an `epub:type` attribute inside an EPUB navigation document, it follows the restricted EPUB `<nav>` content model along with several `landmarks`-specific requirements:

- `<nav epub:type="landmarks">`
  - : The container element for the landmarks list. At most one `<nav epub:type="landmarks">` element may appear in the navigation document, and an ancestor element (typically the root `<html>` element) must declare the EPUB namespace: `xmlns:epub="http://www.idpf.org/2007/ops"`.

    Inside `<nav epub:type="landmarks">`, only two direct child elements are permitted, in order:
    1. An optional heading element ([`<h1>`–`<h6>`](/en-US/docs/Web/HTML/Reference/Elements/Heading_Elements)).
    2. **Exactly one** ordered list ([`<ol>`](/en-US/docs/Web/HTML/Reference/Elements/ol)). Unordered lists ([`<ul>`](/en-US/docs/Web/HTML/Reference/Elements/ul)) are not allowed.

- [`<h1>`–`<h6>`](/en-US/docs/Web/HTML/Reference/Elements/Heading_Elements) (optional)
  - : A single heading that titles the landmarks block (for example, `<h2>Landmarks</h2>` or `<h2>Guide</h2>`). If `<nav epub:type="landmarks">` is rendered visibly rather than marked `hidden=""`, pair the heading's `id` with `aria-labelledby` on `<nav>` (or set `aria-label` on `<nav>`) so assistive technologies can distinguish the landmarks navigation region from the table of contents `<nav>` region.
- [`<ol>`](/en-US/docs/Web/HTML/Reference/Elements/ol)
  - : Contains one or more [`<li>`](/en-US/docs/Web/HTML/Reference/Elements/li) elements. Unlike `<nav epub:type="toc">`, the landmarks list should be **flat**—containing only a single `<ol>` with no nested `<ol>` sublists (and therefore no unlinked `<span>` group headings). Default list styling inside `<nav>` elements is equivalent to `list-style: none`.
- [`<li>`](/en-US/docs/Web/HTML/Reference/Elements/li) and [`<a>`](/en-US/docs/Web/HTML/Reference/Elements/a)
  - : Each `<li>` contains an `<a>` element pointing to a structural section of the publication:
    - **Required `epub:type` attribute**: Every `<a>` element inside `<nav epub:type="landmarks">` **must** include an `epub:type` attribute declaring the structural semantics of its target (typically using terms from the [EPUB 3 Structural Semantics Vocabulary](https://www.w3.org/TR/epub-ssv-11/)).
    - **Internal `href` target**: The `href` attribute must resolve to a content document inside the EPUB container (or a fragment identifier within one, such as `#toc` or `text/backmatter.xhtml#glossary`), never to an external `http:` or `https:` URL. Any content document linked from `landmarks` must also be listed in the package [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).
    - **No duplicate targets with the same role**: `<nav epub:type="landmarks">` must not include multiple entries that have the same `epub:type` value and point to the same resource or fragment.
    - **Human-readable text label**: Every `<a>` element must provide a clear, non-empty, human-readable text label, because reading systems and assistive technologies may display landmark links directly to readers in a menu.

## Common landmark `epub:type` values

Limit `<nav epub:type="landmarks">` to major reference points that reading systems or readers are likely to jump to directly. Common `epub:type` values from the [EPUB 3 Structural Semantics Vocabulary](https://www.w3.org/TR/epub-ssv-11/) include:

- `bodymatter`
  - : Marks the start of the publication's main content (typically Chapter 1, after the cover and frontmatter). This is the most widely acted-upon landmark in practice: platforms such as Apple Books and Amazon Kindle (when converting EPUBs) use `bodymatter` to choose the initial opening location when a reader opens the book for the first time, skipping past copyright and dedication pages.
- `toc`
  - : Points to the in-book [table of contents](/en-US/docs/Related/EPUB/Navigation/TOC) when `nav.xhtml` (or another TOC document) is included in the [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).
- `cover`
  - : Points to the [XHTML](/en-US/docs/Glossary/XHTML) or [SVG](/en-US/docs/Web/SVG) cover document in the `<spine>` (not directly to a raw JPEG or PNG image file).
- `glossary`, `index`, and `bibliography`
  - : Identify key reference sections in the backmatter so reading systems can offer direct menu shortcuts to them.
- `loi` and `lot`
  - : Identify a List of Illustrations (`loi`) or List of Tables (`lot`).
- `frontmatter`, `preface`, `introduction`, `acknowledgments`, and `copyright-page`
  - : Identify specific frontmatter sections when quick access to them is useful.

> [!NOTE]
> Reading system support for `landmarks` varies. While `bodymatter` (along with `cover` and `toc`) triggers built-in behaviors such as setting the initial reading position on several major platforms, some reading systems expose landmarks in a dedicated "Guide" or "Landmarks" menu tab, and others ignore unfamiliar `epub:type` terms.

## Hiding landmarks in the reading order

When you include `nav.xhtml` in the package [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine) so that `<nav epub:type="toc">` appears as a visible table of contents page inside the book, you typically do not want the `landmarks` list rendered on that page as well.

Add the HTML [`hidden`](/en-US/docs/Web/HTML/Reference/Global_attributes/hidden) attribute to `<nav epub:type="landmarks">` to exclude it from rendering in the `<spine>` reading flow:

```xml
<nav epub:type="landmarks" hidden="">
```

The `hidden` attribute only suppresses visual and sequential rendering when `nav.xhtml` is viewed as a page in the `<spine>`—reading systems still parse `<nav epub:type="landmarks">` and expose its entries in their built-in navigation interfaces. Because `nav.xhtml` is an XML document (`application/xhtml+xml`), always write `hidden=""` or `hidden="hidden"` rather than the bare HTML boolean shorthand `hidden`.

## Legacy `<guide>` element

In EPUB 2, structural landmarks were declared inside the `.opf` [package file](/en-US/docs/Related/EPUB/Package) using the `<guide>` element and child `<reference>` elements (such as `<reference type="text" title="Start" href="chapter-1.xhtml" />`).

In EPUB 3, `<nav epub:type="landmarks">` replaces `<guide>`. EPUB 3 reading systems ignore `<guide>`, so you only need to include it if you are specifically targeting legacy EPUB 2 devices.

## Examples

### Navigation document with TOC and hidden landmarks

The following example shows a `nav.xhtml` document containing both the required `<nav epub:type="toc">` element and an optional `<nav epub:type="landmarks" hidden="">` element pointing to the cover page, the table of contents, the start of the main text (`bodymatter`), a list of illustrations (`loi`), and a glossary:

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
- [EPUB 3.3: Legacy `guide` element](https://www.w3.org/TR/epub-33/#sec-opf2-guide)
- [EPUB 3 Structural Semantics Vocabulary 1.1](https://www.w3.org/TR/epub-ssv-11/)

## See also

- [EPUB navigation](/en-US/docs/Related/EPUB/Navigation)
- [EPUB table of contents (TOC)](/en-US/docs/Related/EPUB/Navigation/TOC)
- [EPUB page list](/en-US/docs/Related/EPUB/Navigation/Page-list)
- [EPUB package spine](/en-US/docs/Related/EPUB/Package/Spine)
- [`<nav>`: The HTML Navigation Section element](/en-US/docs/Web/HTML/Reference/Elements/nav)
