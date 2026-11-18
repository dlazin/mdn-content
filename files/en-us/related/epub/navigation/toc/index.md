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

Every EPUB [navigation document](/en-US/docs/Related/EPUB/Navigation) must contain one `<nav epub:type="toc">` element. Reading systems use it to build their table of contents menu, and you can also include `nav.xhtml` in the package [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine) to display the TOC as a page inside the book.

## TOC vs. the package spine

`<nav epub:type="toc">` and the package [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine) serve different roles:

- `<spine>` lists every viewable content document in the sequence the reading system loads them as pages turn.
- `<nav epub:type="toc">` is an outline for the reader. You can leave out minor frontmatter files (such as a cover or copyright page) and link to multiple subheadings inside a single chapter file using fragments (such as `chapter-1.xhtml#section-2`).

Links in the TOC should follow the same order as the documents in `<spine>` and the elements inside each document.

## Structure and elements

Unlike `<nav>` on an ordinary webpage, `<nav epub:type="toc">` follows a restricted HTML structure so reading systems can turn it into native collapsible menus:

- `<nav epub:type="toc">`
  - : Container for the table of contents. Declare `xmlns:epub="http://www.idpf.org/2007/ops"` on `<html>`. When `nav.xhtml` is rendered in `<spine>`, adding [`role="doc-toc"`](https://www.w3.org/TR/dpub-aria-1.1/#doc-toc) exposes the table of contents role to screen readers. Contains an optional heading followed by a single `<ol>`.
- [`<h1>`–`<h6>`](/en-US/docs/Web/HTML/Reference/Elements/Heading_Elements)
  - : Optional heading for the TOC (such as `<h1 id="toc-heading">Contents</h1>`). Reference its `id` with `aria-labelledby` on `<nav>` (or set `aria-label` on `<nav>` if there is no heading) so assistive technologies announce a label for the navigation region.
- [`<ol>`](/en-US/docs/Web/HTML/Reference/Elements/ol)
  - : A level in the TOC hierarchy containing one or more `<li>` elements. Unordered lists (`<ul>`) are not allowed. Default styling inside EPUB `<nav>` elements is `list-style: none`, so list items are not numbered unless styled with CSS.
- [`<li>`](/en-US/docs/Web/HTML/Reference/Elements/li)
  - : A single TOC entry. Must start with one `<a>` or `<span>`, optionally followed by one nested `<ol>` for subsections (required after `<span>`).
- [`<a>`](/en-US/docs/Web/HTML/Reference/Elements/a)
  - : Links to a chapter or section in the EPUB (never an external URL). Every linked content document must be listed in `<spine>`, and `<a>` must have a non-empty text label.
- [`<span>`](/en-US/docs/Web/HTML/Reference/Elements/span)
  - : An unlinked group heading (such as `<span>Part I</span>` when Part I has no separate page). Must have a non-empty text label and must be followed by a nested `<ol>`.

> [!NOTE]
> Many reading apps strip inline HTML tags, CSS styling, and images when rendering their built-in TOC menu. Make sure every TOC entry makes sense as plain text, and add `alt` (and `title` if needed) whenever an entry uses an image.

## Displaying and trimming the TOC in `<spine>`

If you include `nav.xhtml` in `<spine>` so readers see an in-book Contents page, you can use the HTML [`hidden`](/en-US/docs/Web/HTML/Reference/Global_attributes/hidden) attribute (`hidden=""` in XML syntax) to control what appears on the page:

- Add `hidden=""` to deeply nested `<ol>` sublists so the in-book TOC page shows only top-level chapters while the reading app's expandable menu still includes every subsection.
- Add `hidden=""` to sibling [`landmarks`](/en-US/docs/Related/EPUB/Navigation/Landmarks) or [`page-list`](/en-US/docs/Related/EPUB/Navigation/Page-list) `<nav>` elements so they do not appear on the visible TOC page.

## Examples

### Minimal TOC document

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

Declare this file in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) with `properties="nav"`:

```xml
<manifest>
  <item id="nav" href="nav.xhtml" media-type="application/xhtml+xml" properties="nav" />
  <!-- other manifest items -->
</manifest>
```

### Multi-level TOC with an unlinked group heading and hidden sublists

This example uses `<span>` for an unlinked part heading, nested `<ol>` lists for subsections, and `hidden=""` on a third-level `<ol>` to keep third-level headings out of the in-book TOC page while keeping them in the app's TOC menu:

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

- [EPUB navigation document](/en-US/docs/Related/EPUB/Navigation)
- [EPUB landmarks](/en-US/docs/Related/EPUB/Navigation/Landmarks)
- [EPUB page list](/en-US/docs/Related/EPUB/Navigation/Page-list)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
- [EPUB package spine](/en-US/docs/Related/EPUB/Package/Spine)
- [`<nav>` HTML element](/en-US/docs/Web/HTML/Reference/Elements/nav)
