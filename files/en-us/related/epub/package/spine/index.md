---
title: EPUB package spine
short-title: Spine
slug: Related/EPUB/Package/Spine
page-type: guide
sidebar: epub
---

The `<spine>` element is the third child of `<package>` in an EPUB [package file](/en-US/docs/Related/EPUB/Package), placed after the [metadata](/en-US/docs/Related/EPUB/Package/Metadata) and [manifest](/en-US/docs/Related/EPUB/Package/Manifest). It sets the book's default reading order using an ordered list of `<itemref>` elements that point to content documents in the manifest.

While the [manifest](/en-US/docs/Related/EPUB/Package/Manifest) lists every file in the EPUB in any order, the spine lists only the viewable content documents (XHTML or SVG) in the sequence a reader encounters them when turning pages from start to finish.

## Spine vs. navigation

Because both list parts of the book in order, it is easy to confuse the spine with the [navigation document](/en-US/docs/Related/EPUB/Navigation):

- The spine tells the reading system which file to open first and which file comes next when the reader turns past the end of a chapter. Every content document reachable by links or navigation—including a cover page, copyright page, dedication, or standalone notes file—must appear in the spine.
- The navigation document provides the reader-facing [table of contents](/en-US/docs/Related/EPUB/Navigation/TOC). One XHTML file in the spine can contain multiple subheadings linked separately in the table of contents, and minor frontmatter files in the spine (such as a half-title or copyright page) can be omitted from the table of contents.

## Attributes

`<spine>` accepts three optional attributes:

- `id`
  - : An XML identifier for the `<spine>` element.
- `page-progression-direction`
  - : Sets the global direction in which pages advance across the book:
    - `ltr`: Left-to-right (used for English and most left-to-right scripts).
    - `rtl`: Right-to-left (used for Arabic, Hebrew, and vertically typeset Japanese or Chinese books).
    - `default`: Default when omitted; lets the reading system choose page progression automatically.
- `toc`
  - : Legacy EPUB 2 attribute referencing the manifest `id` of an NCX file (`application/x-dtbncx+xml`). Optional in EPUB 3 and used only for backward compatibility with older EPUB 2 devices.

## The `<itemref>` element

The spine contains one or more self-closing `<itemref>` elements:

```xml
<itemref idref="chapter-1" />
```

### `<itemref>` attributes

- `idref`
  - : Required. Matches the `id` of an item in the [manifest](/en-US/docs/Related/EPUB/Package/Manifest). Each manifest `id` can be referenced at most once in the spine.

    The referenced item must be an EPUB content document (`application/xhtml+xml` or `image/svg+xml`, or another format with a manifest `fallback` chain resolving to one). Stylesheets, fonts, and raster images cannot be placed directly in the spine without fallbacks; to show a JPEG cover at the start of the book, wrap it in an XHTML or SVG cover document and reference that document in the spine.
- `linear`
  - : Optional (`yes` or `no`; defaults to `yes`). At least one item in the spine must be linear.
    - `yes`: Part of the normal page-turning sequence.
    - `no`: Marks supplementary material—such as an answer key or standalone endnotes file—meant to be opened from a hyperlink rather than during sequential page turns. You must provide links (in content or navigation) to every `linear="no"` document, and placing `linear="no"` items at the end of the spine is recommended because some reading systems ignore `linear="no"` and include them in normal page turns.
- `properties`
  - : Optional. Space-separated spine property tokens. In reflowable books, `page-spread-left` and `page-spread-right` ask the reading system to start the document on the left-hand or right-hand side when rendering a two-page spread.
- `id`
  - : Optional XML identifier for the `<itemref>` element.

## Examples

### Minimal spine

```xml
<manifest>
  <item id="nav" href="nav.xhtml" media-type="application/xhtml+xml" properties="nav" />
  <item id="css" href="styles/main.css" media-type="text/css" />
  <item id="cover-page" href="text/cover.xhtml" media-type="application/xhtml+xml" />
  <item id="frontmatter" href="text/frontmatter.xhtml" media-type="application/xhtml+xml" />
  <item id="ch1" href="text/chapter-1.xhtml" media-type="application/xhtml+xml" />
  <item id="ch2" href="text/chapter-2.xhtml" media-type="application/xhtml+xml" />
</manifest>
<spine>
  <itemref idref="cover-page" />
  <itemref idref="frontmatter" />
  <itemref idref="ch1" />
  <itemref idref="ch2" />
</spine>
```

### Spine with page-spread hints and non-linear endnotes

```xml
<spine page-progression-direction="ltr">
  <itemref idref="cover-page" />
  <itemref idref="title-page" properties="page-spread-right" />
  <itemref idref="copyright" />
  <itemref idref="ch1" properties="page-spread-right" />
  <itemref idref="ch2" />
  <itemref idref="endnotes" linear="no" />
</spine>
```

## Specifications

- [EPUB 3.3: The `spine` element](https://www.w3.org/TR/epub-33/#sec-spine-elem)
- [EPUB 3.3: The `itemref` element](https://www.w3.org/TR/epub-33/#sec-itemref-elem)
- [EPUB 3.3: Spine properties vocabulary](https://www.w3.org/TR/epub-33/#app-itemref-properties-vocab)

## See also

- [EPUB package file](/en-US/docs/Related/EPUB/Package)
- [EPUB package metadata](/en-US/docs/Related/EPUB/Package/Metadata)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
- [EPUB navigation document](/en-US/docs/Related/EPUB/Navigation)
