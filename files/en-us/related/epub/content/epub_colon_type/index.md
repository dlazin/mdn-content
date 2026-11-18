---
title: "EPUB `epub:type` attribute"
short-title: "epub:type"
slug: Related/EPUB/Content/epub:type
page-type: guide
sidebar: epub
---

The `epub:type` attribute adds publishing semantics—such as marking a footnote, a page break, or the table of contents—to elements in EPUB [XHTML](/en-US/docs/Related/EPUB/Content/XHTML), [SVG](/en-US/docs/Related/EPUB/Content/SVG), and [Media Overlay](/en-US/docs/Related/EPUB/Content/Audio) documents:

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

Although the [EPUB 3 Structural Semantics Vocabulary](https://www.w3.org/TR/epub-ssv-11/) defines dozens of terms (for chapters, prologues, dedications, epigraphs, glossaries, and more), reading systems ignore most `epub:type` values in ordinary chapters. In practice, `epub:type` triggers reading-system behavior in only a few places: pop-up notes, [navigation documents](/en-US/docs/Related/EPUB/Navigation) ([`toc`](/en-US/docs/Related/EPUB/Navigation/TOC), [`landmarks`](/en-US/docs/Related/EPUB/Navigation/Landmarks), and [`page-list`](/en-US/docs/Related/EPUB/Navigation/Page-list)), static page breaks, and Media Overlays.

## Syntax and namespace rules

- Declare `xmlns:epub="http://www.idpf.org/2007/ops"` on the element or an ancestor (usually `<html>` or `<svg>`). Using `epub:type` without declaring `xmlns:epub` causes a fatal XML parsing error.
- In XHTML documents, `epub:type` is allowed on `<body>` and any element inside `<body>`, never on `<head>` or elements inside `<head>`. In SVG, place it only on renderable elements. In SMIL Media Overlays, it is allowed on `<body>`, `<seq>`, and `<par>`.
- Separate multiple terms with spaces (for example, `epub:type="bodymatter chapter"`). Unprefixed terms come from the [EPUB 3 Structural Semantics Vocabulary](https://www.w3.org/TR/epub-ssv-11/).
- `epub:type` does not change native HTML behavior: adding `epub:type="noteref"` to a `<span>` does not make it a clickable link.
- Custom vocabularies can be declared on the root element with `epub:prefix` (for example, `epub:prefix="z3998: https://www.daisy.org/z3998/2012/vocab/structure/"` for `epub:type="z3998:poem"`), though almost all ebooks need only unprefixed default terms. Reading systems ignore unrecognized terms.

## Where reading systems use `epub:type`

### Pop-up footnotes and endnotes

Interactive notes are the main `epub:type` feature supported in chapters across Amazon Kindle, Apple Books, Google Play Books, Kobo, and Readium-based apps (which converts EPUB 3 note markup into pop-up notes during ingestion):

- `noteref`
  - : Placed on an [`<a>`](/en-US/docs/Web/HTML/Reference/Elements/a) link in the body text pointing to the note's `id` (in the same file or a separate notes file).
- `footnote` or `endnote` (and legacy `rearnote`)
  - : Placed on the note target—most reliably an [`<aside>`](/en-US/docs/Web/HTML/Reference/Elements/aside) element—so the reading system can show the note in a pop-up card without leaving the current page.
- `backlink`
  - : Placed on a return `<a>` link inside the note for reading systems that jump to the note instead of opening a pop-up.

### Navigation documents, page breaks, and Media Overlays

- Inside `nav.xhtml`, `epub:type` identifies [`toc`](/en-US/docs/Related/EPUB/Navigation/TOC), [`landmarks`](/en-US/docs/Related/EPUB/Navigation/Landmarks) (plus landmark target types such as `bodymatter`, `cover`, and `index` on child `<a>` links), and [`page-list`](/en-US/docs/Related/EPUB/Navigation/Page-list).
- In chapters, `pagebreak` marks static print page boundaries targeted by `<nav epub:type="page-list">`.
- In Media Overlays, terms such as `pagebreak`, `footnote`, `figure`, and `table` let listeners skip optional structures or escape nested ones during synchronized audio playback.

Most other vocabulary terms—such as `prologue`, `epilogue`, `dedication`, `epigraph`, `part`, or `chapter`—produce no UI changes in mainstream reading apps, though publishers sometimes keep them for internal editorial or conversion workflows.

## `epub:type` vs. ARIA `role` attributes

> [!IMPORTANT]
> Screen readers ignore `epub:type`. Because `epub:type` does not map to browser or operating-system accessibility APIs, adding `epub:type` alone does not expose footnotes, page breaks, or sections to assistive technologies.

`epub:type` and ARIA [`role`](/en-US/docs/Web/Accessibility/ARIA/Reference/Roles) attributes serve different consumers:

- `epub:type` drives EPUB reading system features (such as pop-up footnotes and built-in navigation menus).
- `role`—specifically roles from the [Digital Publishing WAI-ARIA Module (DPUB-ARIA)](https://www.w3.org/TR/dpub-aria-1.1/)—exposes structural semantics to screen readers.

On visible elements that benefit both reading systems and assistive technologies, include both attributes:

| Structure                   | Element             | `epub:type` (reading systems)         | `role` (assistive technologies) |
| --------------------------- | ------------------- | ------------------------------------- | ------------------------------- |
| Visible table of contents   | `<nav>`             | `epub:type="toc"`                     | `role="doc-toc"`                |
| Visible page list           | `<nav>`             | `epub:type="page-list"`               | `role="doc-pagelist"`           |
| Static page break           | `<span>` or `<div>` | `epub:type="pagebreak"`               | `role="doc-pagebreak"`          |
| Note reference link         | `<a>`               | `epub:type="noteref"`                 | `role="doc-noteref"`            |
| Individual note (`<aside>`) | `<aside>`           | `epub:type="footnote"` (or `endnote`) | `role="doc-footnote"`           |
| Note return link            | `<a>`               | `epub:type="backlink"`                | `role="doc-backlink"`           |
| Endnotes section            | `<section>`         | `epub:type="endnotes"`                | `role="doc-endnotes"`           |

Keep three ARIA details in mind:

- Do not use singular `role="doc-endnote"` (or `role="doc-biblioentry"`), which are deprecated in DPUB-ARIA 1.1. For a collected endnotes section, put `epub:type="endnotes" role="doc-endnotes"` on `<section>` and use a standard `<ol>` and `<li>` list inside; for standalone `<aside>` notes, `role="doc-footnote"` applies to individual notes regardless of placement.
- Omit `role` attributes on `<nav>` elements marked with `hidden=""`, because `hidden` removes the element from the accessibility tree.
- Avoid adding ARIA roles to every minor workflow element, which creates verbose announcements for screen reader users. Rely on native HTML elements (`<section>`, `<aside>`, `<nav>`, `<figure>`) first.

## Examples

### Chapter with a pop-up footnote and static page break

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
      <span id="page-1" epub:type="pagebreak" role="doc-pagebreak" aria-label="1"></span>
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
- [ARIA roles](/en-US/docs/Web/Accessibility/ARIA/Reference/Roles)
