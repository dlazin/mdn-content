---
title: EPUB table of contents (TOC)
short-title: Table of contents
slug: Related/EPUB/Navigation/TOC
page-type: guide
sidebar: epub
---

In EPUB 3, the **table of contents (TOC)** is an HTML [`<nav>`](/en-US/docs/Web/HTML/Reference/Elements/nav) element with `epub:type="toc"` inside the book's [navigation document](/en-US/docs/Related/EPUB/Navigation) (`nav.xhtml`):

```xml
<nav epub:type="toc" role="doc-toc" aria-labelledby="toc-heading" id="toc">
  <h1 id="toc-heading">Contents</h1>
  <ol>
    <li><a href="text/chapter-1.xhtml">Chapter 1: Loomings</a></li>
    <li><a href="text/chapter-2.xhtml">Chapter 2: The Carpet-Bag</a></li>
  </ol>
</nav>
```

Every EPUB [navigation document](/en-US/docs/Related/EPUB/Navigation) must contain exactly one `<nav epub:type="toc">` element. Links in the TOC should follow the same order as the documents in [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine) and the sections within each document, though the TOC can omit minor frontmatter files and link to multiple subheadings inside a single chapter (see [Spine vs. navigation](/en-US/docs/Related/EPUB/Package/Spine#spine_vs_navigation)).

## Structure and elements

`<nav epub:type="toc">` follows the [EPUB `<nav>` content rules](/en-US/docs/Related/EPUB/Navigation#content_rules_for_nav_elements) and builds a single-level or nested outline from these elements:

- `<nav epub:type="toc">`
  - : Container for the table of contents. When `nav.xhtml` is rendered visibly in `<spine>` (without `hidden=""`), include [`role="doc-toc"`](https://www.w3.org/TR/dpub-aria-1.1/#doc-toc) and label the region with `aria-labelledby` (pointing to the heading's `id`) or `aria-label` for screen readers.
- [`<h1>`–`<h6>`](/en-US/docs/Web/HTML/Reference/Elements/Heading_Elements)
  - : Optional heading for the TOC (such as `<h1 id="toc-heading">Contents</h1>`).
- [`<ol>`](/en-US/docs/Web/HTML/Reference/Elements/ol)
  - : A level in the TOC hierarchy containing one or more `<li>` elements. Add [`hidden=""`](/en-US/docs/Web/HTML/Reference/Global_attributes/hidden) to deeply nested `<ol>` sublists if you want to omit lower-level headings from the visible [in-book TOC page](/en-US/docs/Related/EPUB/Navigation#including_nav_xhtml_in_the_reading_order) while keeping them in the reading app's expandable TOC menu.
- [`<li>`](/en-US/docs/Web/HTML/Reference/Elements/li)
  - : A single TOC entry, starting with one `<a>` or `<span>` and optionally followed by one nested `<ol>` for subsections (required after `<span>`).
- [`<a>`](/en-US/docs/Web/HTML/Reference/Elements/a)
  - : Links to a chapter or `#` fragment in a `<spine>` document, with a non-empty text label.
- [`<span>`](/en-US/docs/Web/HTML/Reference/Elements/span)
  - : An unlinked group heading (such as `<span>Part I</span>` when Part I has no dedicated page), followed by a nested `<ol>`.

## Examples

### Multi-level TOC with an unlinked group heading and hidden sublists

This example uses `<span>` for an unlinked part heading, nested `<ol>` lists for subsections, and `hidden=""` on a third-level `<ol>` to hide third-level headings on the in-book Contents page while preserving them in the app's TOC menu:

```xml
<nav epub:type="toc" role="doc-toc" aria-labelledby="toc-heading" id="toc">
  <h1 id="toc-heading">Table of Contents</h1>
  <ol>
    <li>
      <span>Part I: On Shore</span>
      <ol>
        <li>
          <a href="text/chapter-1.xhtml">Chapter 1: Loomings</a>
          <ol>
            <li>
              <a href="text/chapter-1.xhtml#sec-1-1">Manhattan</a>
              <ol hidden="">
                <li><a href="text/chapter-1.xhtml#sec-1-1-1">The Battery</a></li>
                <li><a href="text/chapter-1.xhtml#sec-1-1-2">Corlears Hook</a></li>
              </ol>
            </li>
            <li><a href="text/chapter-1.xhtml#sec-1-2">Going to Sea</a></li>
          </ol>
        </li>
        <li><a href="text/chapter-2.xhtml">Chapter 2: The Carpet-Bag</a></li>
      </ol>
    </li>
    <li>
      <a href="text/part-2.xhtml">Part II: At Sea</a>
      <ol>
        <li><a href="text/chapter-3.xhtml">Chapter 3: The Pequod</a></li>
      </ol>
    </li>
  </ol>
</nav>
```

## Specifications

- [EPUB 3.3: The `toc nav` element](https://www.w3.org/TR/epub-33/#sec-nav-toc)
- [EPUB 3.3: The `nav` element: restrictions](https://www.w3.org/TR/epub-33/#sec-nav-def-model)
- [EPUB 3.3: Using in the spine](https://www.w3.org/TR/epub-33/#sec-nav-doc-use-spine)

## See also

- [EPUB navigation document](/en-US/docs/Related/EPUB/Navigation)
- [EPUB landmarks](/en-US/docs/Related/EPUB/Navigation/Landmarks)
- [EPUB page list](/en-US/docs/Related/EPUB/Navigation/Page-list)
- [EPUB package spine](/en-US/docs/Related/EPUB/Package/Spine)
- [`<nav>` HTML element](/en-US/docs/Web/HTML/Reference/Elements/nav)
