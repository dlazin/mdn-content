---
title: "EPUB `epub:type` attribute"
short-title: "epub:type"
slug: Related/EPUB/Content/epub:type
page-type: guide
sidebar: epub
---

The **`epub:type`** attribute attaches publishing-specific structural semantics—such as marking a footnote, a page break, or the table of contents—to elements in EPUB [XHTML](/en-US/docs/Glossary/XHTML) and [SVG](/en-US/docs/Web/SVG) content documents:

```xml
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:epub="http://www.idpf.org/2007/ops"
      xml:lang="en"
      lang="en">
  <body>
    <p>
      Call me Ishmael.<a id="ref1" epub:type="noteref" role="doc-noteref" href="#fn1">1</a>
    </p>
    <aside id="fn1" epub:type="footnote" role="doc-footnote">
      <p><a epub:type="backlink" role="doc-backlink" href="#ref1">1.</a> The narrator of <em>Moby-Dick</em>.</p>
    </aside>
  </body>
</html>
```

Although the [EPUB 3 Structural Semantics Vocabulary](https://www.w3.org/TR/epub-ssv-11/) defines dozens of terms (for chapters, prologues, dedications, epigraphs, glossaries, and more), **reading systems ignore most `epub:type` values in regular content documents**. In practice, `epub:type` only triggers visible reading-system behavior in a few specific places—most notably **pop-up footnotes and endnotes** and **navigation documents** ([`toc`](/en-US/docs/Related/EPUB/Navigation/TOC), [`landmarks`](/en-US/docs/Related/EPUB/Navigation/Landmarks), and [`page-list`](/en-US/docs/Related/EPUB/Navigation/Page-list)).

## Syntax and namespace rules

- **XML namespace declaration**: Because `epub:type` is a namespaced XML attribute, the element or one of its ancestors (typically the root `<html>` or `<svg>` element) must declare `xmlns:epub="http://www.idpf.org/2007/ops"`. Using `epub:type` without declaring `xmlns:epub` causes a fatal XML parsing error in EPUB reading systems.
- **Allowed elements**:
  - In [XHTML](/en-US/docs/Glossary/XHTML) content documents, `epub:type` can be placed on `<body>` or any element inside `<body>`, but **must not** be placed on `<head>` or metadata elements inside `<head>`.
  - In [SVG](/en-US/docs/Web/SVG) content documents, `epub:type` may only be placed on renderable elements.
  - In SMIL Media Overlay documents, `epub:type` is allowed on `<body>`, `<seq>`, and `<par>` elements.
- **Values and multiple terms**: The value is a whitespace-separated list of one or more property tokens (for example, `epub:type="bodymatter chapter"`). Unprefixed terms belong to the default vocabulary, the [EPUB 3 Structural Semantics Vocabulary](https://www.w3.org/TR/epub-ssv-11/).
- **Native element behavior takes precedence**: `epub:type` refines the meaning of an element without overriding its native HTML semantics—for example, adding `epub:type="noteref"` to a `<span>` does not turn it into a clickable hyperlink.
- **Custom vocabularies (`epub:prefix`)**: Almost all ebooks only need unprefixed terms from the default vocabulary. If a publishing workflow requires terms from another vocabulary, declare the prefix on the root element with `epub:prefix` (for example, `epub:prefix="z3998: https://www.daisy.org/z3998/2012/vocab/structure/"`) and reference prefixed terms such as `epub:type="z3998:poem"`. Reading systems ignore any `epub:type` terms they do not recognize.

## Which `epub:type` values reading systems actually use

While the specification allows reading systems to attach custom behaviors to any vocabulary term, real-world reading system support is concentrated in a small set of features:

### Pop-up footnotes and endnotes

Outside the navigation document, interactive notes are the main `epub:type` feature implemented across major reading systems—including Apple Books, Google Play Books, Kobo, Readium-based apps, and Amazon Kindle (which converts EPUB 3 note markup into pop-up notes during Kindle Direct Publishing ingestion):

- `noteref`: Placed on an [`<a>`](/en-US/docs/Web/HTML/Reference/Elements/a) element in the main text whose `href` points to the note's `id` (either in the same file or in a separate notes file).
- `footnote` or `endnote` (and `rearnote`): Placed on the note target—most reliably an [`<aside>`](/en-US/docs/Web/HTML/Reference/Elements/aside) element for individual notes—so the reading system can extract the note's content and display it in a pop-up card or modal overlay without navigating the reader away from the current page.
- `backlink`: Placed on an `<a>` link inside a note that returns the reader to the inline reference in reading systems that jump to the note instead of showing a pop-up.

### Navigation documents

Inside `nav.xhtml`, reading systems rely on `epub:type` to identify each navigation block and landmark target:

- [`toc`](/en-US/docs/Related/EPUB/Navigation/TOC): Required on `<nav epub:type="toc">` to identify the publication's primary table of contents.
- [`landmarks`](/en-US/docs/Related/EPUB/Navigation/Landmarks): Identifies `<nav epub:type="landmarks">`, where every child `<a>` element also requires an `epub:type` value—most notably `bodymatter` (used by platforms such as Apple Books and Kindle conversion to set the initial opening page), `cover`, `toc`, `glossary`, and `index`.
- [`page-list`](/en-US/docs/Related/EPUB/Navigation/Page-list): Identifies `<nav epub:type="page-list">` for static print-equivalent page navigation.

### Page breaks and media overlays

- `pagebreak`: Marks static page-break anchor elements (`<span id="page-1" epub:type="pagebreak" ...>`) targeted by `<nav epub:type="page-list">`.
- **Media Overlays skippability and escapability**: In synchronized audio books (SMIL Media Overlays) or built-in read-aloud modes, reading systems can use terms such as `pagebreak`, `footnote`, `figure`, or `table` on `<seq>`/`<par>` or XHTML elements so listeners can skip optional structures or escape deeply nested ones.

### Workflow-only terms

Most other terms in the vocabulary—such as `prologue`, `epilogue`, `dedication`, `acknowledgments`, `epigraph`, `part`, `chapter`, or `glossary` on `<section>` elements—produce no special UI behavior in mainstream reading systems today. Publishers sometimes include them for internal editorial workflows, semantic archiving, or automated conversion pipelines, though standard HTML elements and `class` attributes handle visual styling more portably.

## `epub:type` vs. ARIA `role` attributes

> [!IMPORTANT]
> **Screen readers ignore `epub:type`.** Because `epub:type` does not map to browser or operating-system accessibility APIs, adding `epub:type` alone does not make footnotes, page breaks, or sections recognizable to assistive technologies.

`epub:type` and WAI-ARIA [`role`](/en-US/docs/Web/Accessibility/ARIA/Reference/Roles) attributes serve two separate audiences:

- **`epub:type`** drives **EPUB reading system UI features** (such as pop-up footnote cards and built-in TOC/page-list menus).
- **`role`** (specifically roles from the [Digital Publishing WAI-ARIA Module](https://www.w3.org/TR/dpub-aria-1.1/)) exposes structural semantics to **screen readers and other assistive technologies**.

When marking up structures that benefit both reading systems and assistive technologies, include **both** attributes on the element:

| Structure | Element | `epub:type` (reading systems) | `role` (assistive technologies) |
| --- | --- | --- | --- |
| Table of contents | `<nav>` | `epub:type="toc"` | `role="doc-toc"` |
| Page list | `<nav>` | `epub:type="page-list"` | `role="doc-pagelist"` |
| Static page break | `<span>` or `<div>` | `epub:type="pagebreak"` | `role="doc-pagebreak"` |
| Note reference link | `<a>` | `epub:type="noteref"` | `role="doc-noteref"` |
| Individual footnote | `<aside>` | `epub:type="footnote"` | `role="doc-footnote"` |
| Note return link | `<a>` | `epub:type="backlink"` | `role="doc-backlink"` |
| Endnotes section | `<section>` | `epub:type="endnotes"` | `role="doc-endnotes"` |

Avoid mechanically copying every workflow `epub:type` term into an ARIA `role`—overusing ARIA roles on minor elements creates verbose "semantic noise" for screen reader users. Use native HTML sectioning and grouping elements (`<section>`, `<aside>`, `<nav>`, `<figure>`) first, and apply `doc-*` roles where they meaningfully aid non-visual navigation.

## Examples

### Chapter with a pop-up footnote and static page break

The following example shows an XHTML chapter file that declares `xmlns:epub="http://www.idpf.org/2007/ops"` on `<html>` and pairs `epub:type` with `doc-*` ARIA roles for a chapter section, a static page break, and an interactive pop-up footnote:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:epub="http://www.idpf.org/2007/ops"
      xml:lang="en"
      lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Chapter 1: Loomings</title>
    <link rel="stylesheet" type="text/css" href="../styles/main.css" />
  </head>
  <body>
    <section epub:type="bodymatter chapter" role="doc-chapter" aria-labelledby="ch1-title">
      <div id="page-1" epub:type="pagebreak" role="doc-pagebreak" aria-label="1"></div>
      <h1 id="ch1-title">Chapter 1: Loomings</h1>

      <p>
        Call me Ishmael.<a id="ref-1" epub:type="noteref" role="doc-noteref" href="#fn-1">1</a>
        Some years ago—never mind how long precisely—having little or no money in my purse,
        and nothing particular to interest me on shore, I thought I would sail about a little
        and see the watery part of the world.
      </p>

      <aside id="fn-1" epub:type="footnote" role="doc-footnote">
        <p>
          <a epub:type="backlink" role="doc-backlink" href="#ref-1">1.</a>
          Ishmael is the sole survivor of the whaling ship <em>Pequod</em>.
        </p>
      </aside>
    </section>
  </body>
</html>
```

## Specifications

- [EPUB 3.3: The `epub:type` attribute](https://www.w3.org/TR/epub-33/#sec-epub-type-attribute)
- [EPUB 3.3: Structural semantics in XHTML](https://www.w3.org/TR/epub-33/#sec-xhtml-structural-semantics)
- [EPUB 3.3: Vocabulary association mechanisms (`epub:prefix`)](https://www.w3.org/TR/epub-33/#sec-vocab-assoc)
- [EPUB 3 Structural Semantics Vocabulary 1.1](https://www.w3.org/TR/epub-ssv-11/)
- [Digital Publishing WAI-ARIA Module 1.1](https://www.w3.org/TR/dpub-aria-1.1/)

## See also

- [EPUB table of contents (TOC)](/en-US/docs/Related/EPUB/Navigation/TOC)
- [EPUB landmarks](/en-US/docs/Related/EPUB/Navigation/Landmarks)
- [EPUB page list](/en-US/docs/Related/EPUB/Navigation/Page-list)
- [WAI-ARIA Roles](/en-US/docs/Web/Accessibility/ARIA/Reference/Roles)
