---
title: EPUB landmarks
short-title: Landmarks
slug: Related/EPUB/Reference/Navigation/Landmarks
page-type: guide
sidebar: epub
---

In EPUB 3, landmarks are an optional navigation list inside the [navigation document](/en-US/docs/Related/EPUB/Reference/Navigation), marked up with `<nav epub:type="landmarks">`:

```xml
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
```

While the [table of contents](/en-US/docs/Related/EPUB/Reference/Navigation/TOC) outlines chapters for the reader, landmarks are a short, flat list of structural roles for the reading system—letting apps offer quick-jump shortcuts or open the book directly at Chapter 1 (`bodymatter`) on first launch. A navigation document may contain at most one landmarks list. Do not repeat every chapter in the landmarks list.

## Landmark rules

The landmarks list follows the [EPUB `<nav>` content rules](/en-US/docs/Related/EPUB/Reference/Navigation#content_rules_for_nav_elements) with a few landmark-specific requirements:

- Keep `<ol>` as a single flat list of `<li>` entries without nested `<ol>` sublists or `<span>` headings.
- Include an [`epub:type`](/en-US/docs/Related/EPUB/Reference/Content/epub:type) attribute on every `<a>` link, using clear human-readable link text (since some apps show landmark labels directly in menus).
- Do not include two landmark links with both the same `epub:type` value and the same target URL.
- Add [`hidden=""`](/en-US/docs/Web/HTML/Reference/Global_attributes/hidden) to the `<nav>` element when the navigation document is [included in the spine](/en-US/docs/Related/EPUB/Reference/Navigation#including_the_navigation_document_in_the_reading_order) so landmarks do not render on the visible Contents page. Unlike the table of contents and page list, landmarks have no DPUB-ARIA `doc-*` role; if rendered visibly without `hidden=""`, label `<nav>` with `aria-labelledby` or `aria-label`.

## Common landmark `epub:type` values

- `bodymatter`
  - : Start of the main text (usually Chapter 1, after the frontmatter). Platforms such as Amazon Kindle (during EPUB conversion) and Apple Books use `bodymatter` to set the initial opening page when a reader opens the book for the first time.
- `toc`
  - : The in-book [table of contents](/en-US/docs/Related/EPUB/Reference/Navigation/TOC) when the navigation document (or another TOC file) is included in the [spine](/en-US/docs/Related/EPUB/Reference/Package/Spine).
- `cover`
  - : The XHTML or SVG cover page in the spine (not a raw JPEG or PNG image file).
- `glossary`, `index`, `bibliography`
  - : Reference sections in the backmatter.
- `loi`, `lot`
  - : List of illustrations (`loi`) or list of tables (`lot`).
- `frontmatter`, `preface`, `introduction`, `acknowledgments`, `copyright-page`
  - : Key frontmatter sections.

## Specifications

- [EPUB 3.3: The `landmarks nav` element](https://www.w3.org/TR/epub-33/#sec-nav-landmarks)
- [EPUB 3.3: The `nav` element: restrictions](https://www.w3.org/TR/epub-33/#sec-nav-def-model)
- [EPUB 3 Structural Semantics Vocabulary 1.1](https://www.w3.org/TR/epub-ssv-11/)

## See also

- [EPUB page list](/en-US/docs/Related/EPUB/Reference/Navigation/Page-list)
