---
title: EPUB table of contents (TOC)
short-title: Table of contents
slug: Related/EPUB/Navigation/TOC
page-type: guide
sidebar: epub
---

In EPUB 3, the **table of contents (TOC)** is defined by an HTML [`<nav>`](/en-US/docs/Web/HTML/Reference/Elements/nav) element with the `epub:type="toc"` attribute inside the publication's [navigation document](/en-US/docs/Related/EPUB/Navigation) (commonly named `nav.xhtml`):

```xml
<nav epub:type="toc" role="doc-toc" aria-labelledby="toc-heading" id="toc">
  <h1 id="toc-heading">Contents</h1>
  <ol>
    <li><a href="text/chapter-1.xhtml">Chapter 1: Loomings</a></li>
    <li><a href="text/chapter-2.xhtml">Chapter 2: The Carpet-Bag</a></li>
  </ol>
</nav>
```

Every EPUB [navigation document](/en-US/docs/Related/EPUB/Navigation)—declared in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) with `properties="nav"`—must contain **exactly one** `<nav epub:type="toc">` element. Reading systems parse this element to generate their built-in table of contents menu or sidebar, and authors can also include the navigation document in the [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine) so readers encounter the TOC as a page inside the book.

## TOC vs. the package spine

The `<nav epub:type="toc">` element and the package [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine) both reflect the sequence of a book, so it is easy to confuse them:

- **The [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine) controls reading order for the reading system**: It lists every viewable content document in the sequence the e-reader loads them as the user turns pages. Every content document in the book must appear once in the `<spine>`.
- **The TOC provides an outline for the human reader**: Like the table of contents in a printed book, `<nav epub:type="toc">` links to the major sections a reader might want to jump to directly. You can omit minor frontmatter files that appear in the `<spine>` (such as a cover page, half-title page, or copyright page), and you can link to multiple subheadings within a single [XHTML](/en-US/docs/Glossary/XHTML) file using fragment identifiers (such as `chapter-1.xhtml#section-2`).

Although the TOC does not have to include every file in the `<spine>`, its links should follow the same order as the referenced documents in the `<spine>` and the top-to-bottom order of elements within each document.

## Structure and content rules

On a standard webpage, a [`<nav>`](/en-US/docs/Web/HTML/Reference/Elements/nav) element can contain almost any HTML flow content, including paragraphs, `<div>` wrappers, or unordered lists ([`<ul>`](/en-US/docs/Web/HTML/Reference/Elements/ul)). Inside an EPUB navigation document, however, `<nav epub:type="toc">` follows a strict content model so that reading systems can reliably extract its hierarchy for native menus:

- `<nav epub:type="toc">`
  - : The container element for the table of contents. Because `epub:type` uses the EPUB XML namespace, an ancestor element (typically the root `<html>` element) must declare `xmlns:epub="http://www.idpf.org/2007/ops"`. Including `role="doc-toc"` (from the [Digital Publishing WAI-ARIA Module](https://www.w3.org/TR/dpub-aria-1.1/#doc-toc)) is also recommended to expose the table of contents role to assistive technologies.

    Inside `<nav epub:type="toc">`, only two direct child elements are permitted, in order:
    1. An optional heading element ([`<h1>`–`<h6>`](/en-US/docs/Web/HTML/Reference/Elements/Heading_Elements)).
    2. **Exactly one** ordered list ([`<ol>`](/en-US/docs/Web/HTML/Reference/Elements/ol)). Unordered lists (`<ul>`) are not allowed anywhere inside `<nav epub:type="toc">`.

- [`<h1>`–`<h6>`](/en-US/docs/Web/HTML/Reference/Elements/Heading_Elements) (optional)
  - : A single heading that titles the table of contents (for example, `<h1 id="toc-heading">Contents</h1>`). Pair the heading's `id` with `aria-labelledby` on the `<nav>` element (or set `aria-label` directly on `<nav>` if you omit a visible heading) so assistive technologies announce an accessible name for the navigation landmark.
- [`<ol>`](/en-US/docs/Web/HTML/Reference/Elements/ol)
  - : Represents a level in the table of contents hierarchy and must contain one or more [`<li>`](/en-US/docs/Web/HTML/Reference/Elements/li) elements. Even though `<ol>` is an ordered list, the EPUB specification sets the default list styling inside `<nav>` elements to `list-style: none`, so entries are not automatically numbered unless you apply custom CSS for in-spine display.
- [`<li>`](/en-US/docs/Web/HTML/Reference/Elements/li)
  - : Represents a single entry in the table of contents. Each `<li>` must contain, in order:
    1. **Exactly one** [`<a>`](/en-US/docs/Web/HTML/Reference/Elements/a) element (for a linked entry) or [`<span>`](/en-US/docs/Web/HTML/Reference/Elements/span) element (for an unlinked group heading).
    2. Optionally, **one** nested `<ol>` element containing child entries for subsections—except after a `<span>`, where a nested `<ol>` is **required**.
- [`<a>`](/en-US/docs/Web/HTML/Reference/Elements/a)
  - : Links to a section of the publication. Its `href` attribute must resolve to a content document inside the EPUB container (or a fragment identifier within one, such as `text/chapter-1.xhtml#part-2`), never to an external `http:` or `https:` URL. The `<a>` element must provide a non-empty text label (via text content or via `alt` or `title` attributes on embedded elements).
- [`<span>`](/en-US/docs/Web/HTML/Reference/Elements/span)
  - : Provides an unlinked heading that groups a set of child entries (for example, `<span>Part I</span>` when Part I does not have its own separate page). A `<span>` must have a non-empty text label and **must** be followed by a nested `<ol>` inside the same `<li>`; it cannot be used as a standalone leaf item.

> [!NOTE]
> Although `<a>` and `<span>` allow inline HTML phrasing content (such as `<em>`, `<code>`, or `<img>`), many reading systems generate native table of contents widgets that strip HTML markup, CSS styling, and images. Ensure every TOC entry remains clear as plain text, and always include `alt` (and `title`, if needed) text if an entry contains an image.

## Including the TOC in the reading order

Reading systems always build their navigation UI from `<nav epub:type="toc">`, whether or not the navigation document appears in the package [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).

If you also want readers to encounter a formatted table of contents page while paging through the book, reference the navigation document's manifest `id` with an `<itemref>` in the `<spine>`. Reusing `nav.xhtml` in the `<spine>` avoids maintaining two separate tables of contents that could fall out of sync.

When `nav.xhtml` is included in the `<spine>`, you can control what appears on the in-book page using the HTML [`hidden`](/en-US/docs/Web/HTML/Reference/Global_attributes/hidden) attribute:

- **Trimming deep subsections**: If your TOC includes many levels of subsections for the reading system's expandable menu, you can place `hidden=""` (or `hidden="hidden"`) on deeper nested `<ol>` elements so the in-book TOC page displays only the top levels.
- **Hiding machine-oriented navigation lists**: If `nav.xhtml` also contains [`landmarks`](/en-US/docs/Related/EPUB/Navigation/Landmarks) or a [`page-list`](/en-US/docs/Related/EPUB/Navigation/Page-list), adding `hidden=""` to those `<nav>` elements keeps them off the visible in-book TOC page.

The `hidden` attribute only suppresses rendering in the `<spine>` content flow—reading systems still read and expose hidden `<nav>` elements and hidden `<ol>` branches in their built-in navigation menus. Because the navigation document must be valid XML (`application/xhtml+xml`), always write `hidden=""` or `hidden="hidden"` rather than the bare HTML boolean shorthand `hidden`.

## Legacy NCX tables of contents

In EPUB 2, the table of contents was stored in a separate XML format called the Navigation Control file for XML (`.ncx`, typically `toc.ncx` with media type `application/x-dtbncx+xml`) and referenced via the `toc` attribute on [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).

In EPUB 3, `<nav epub:type="toc">` replaces the NCX file. Including an NCX file in an EPUB 3 publication is optional and is only needed if you want to support legacy EPUB 2 reading systems.

## Examples

### Minimal navigation document with a TOC

The following example shows a complete, minimal `nav.xhtml` file containing the required `<nav epub:type="toc">` element:

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
  </body>
</html>
```

To register this file as the publication's navigation document, declare it in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) with `properties="nav"`:

```xml
<manifest>
  <item id="nav" href="nav.xhtml" media-type="application/xhtml+xml" properties="nav" />
  <!-- other manifest items -->
</manifest>
```

### Hierarchical TOC with unlinked headings and hidden sub-branches

The following example shows a multi-level `<nav epub:type="toc">` that uses `<span>` for an unlinked part divider, nested `<ol>` lists for chapter subsections, and `hidden=""` on a third-level `<ol>` so that third-level sections appear in the e-reader's TOC menu without cluttering `nav.xhtml` when rendered in the `<spine>`:

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
- [EPUB 3.3: EPUB navigation document](https://www.w3.org/TR/epub-33/#sec-nav)
- [EPUB 3.3: The `nav` element: restrictions](https://www.w3.org/TR/epub-33/#sec-nav-def-model)
- [EPUB 3.3: Using in the spine](https://www.w3.org/TR/epub-33/#sec-nav-doc-use-spine)

## See also

- [EPUB navigation](/en-US/docs/Related/EPUB/Navigation)
- [EPUB landmarks](/en-US/docs/Related/EPUB/Navigation/Landmarks)
- [EPUB page list](/en-US/docs/Related/EPUB/Navigation/Page-list)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
- [EPUB package spine](/en-US/docs/Related/EPUB/Package/Spine)
- [`<nav>`: The HTML Navigation Section element](/en-US/docs/Web/HTML/Reference/Elements/nav)
