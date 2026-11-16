---
title: EPUB package spine
short-title: Spine
slug: Related/EPUB/Package/Spine
page-type: guide
sidebar: epub
---

The **`<spine>`** element is the third required child of the `<package>` element in an EPUB [package file](/en-US/docs/Related/EPUB/Package) (`.opf`), appearing immediately after [`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata) and [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest). It defines the default reading order of the publication by listing an ordered sequence of `<itemref>` elements that point to content documents declared in the `<manifest>`.

While the [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) is an unordered inventory of every file in the EPUB (including CSS stylesheets, fonts, and images), the `<spine>` lists only the viewable content documents (XHTML or SVG documents) in the exact sequence a reader encounters them when turning pages from start to finish.

## Spine vs. navigation

It is common to confuse the `<spine>` with the [navigation document](/en-US/docs/Related/EPUB/Navigation) (`nav.xhtml`), because both list parts of the book in reading order. However, they serve different purposes:

- **The `<spine>` is for the reading system**: It tells the e-reader which content document to load first and which file comes next when the reader turns past the end of a file. Every content document that can be opened in the book—including a cover page, half-title page, copyright page, dedication, or standalone footnotes file—must be listed in the `<spine>`.
- **The [navigation document](/en-US/docs/Related/EPUB/Navigation) is for the human reader**: It provides the user-facing table of contents (`epub:type="toc"`). A single XHTML file in the `<spine>` can contain multiple chapters or subheadings that each have their own link in the table of contents, and conversely, frontmatter files in the `<spine>` (such as a copyright page) do not have to be listed in the table of contents.

## Attributes

The `<spine>` element accepts the following optional attributes:

- `id`
  - : An optional XML identifier for the `<spine>` element.
- `page-progression-direction`
  - : Sets the global direction in which pages flow across the publication:
    - `ltr`: Left-to-right page progression (used for English and most left-to-right scripts).
    - `rtl`: Right-to-left page progression (used for Arabic, Hebrew, and vertically typeset Japanese or Chinese publications).
    - `default`: The default value when omitted; the reading system determines page progression automatically.
- `toc`
  - : A legacy attribute from EPUB 2 that references the `id` of an NCX navigation file (`application/x-dtbncx+xml`) in the [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest). It is optional in EPUB 3 (which uses the XHTML [navigation document](/en-US/docs/Related/EPUB/Navigation) instead) and is only included when an author wants to provide backward compatibility for legacy EPUB 2 reading systems.

## Child elements

Every `<spine>` element must contain one or more **`<itemref>`** elements. Each `<itemref>` element is an empty (self-closing) XML element that references a single content document in the [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest):

```xml
<itemref idref="chapter-1" />
```

### Required `<itemref>` attributes

- `idref`
  - : Must match the `id` attribute of an `<item>` in the [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest). Two `<itemref>` elements in the same `<spine>` may not reference the same manifest `id`.

    The referenced manifest `<item>` must be an EPUB content document (`application/xhtml+xml` or `image/svg+xml`, or a foreign media type that specifies a manifest `fallback` chain resolving to one). You cannot reference CSS stylesheets, fonts, or raster images directly in an `<itemref>`—for example, to include a JPEG cover image at the front of the book, wrap it in an XHTML or SVG cover document and reference that document's `id` in the `<spine>`.

### Optional `<itemref>` attributes

- `linear`
  - : Indicates whether the referenced document is part of the primary sequential reading order (`yes` or `no`; defaults to `yes`). At least one `<itemref>` in the `<spine>` must have `linear="yes"` (or omit the `linear` attribute).
    - `yes` (default): The document is presented in the normal page-turning sequence of the book.
    - `no`: Marks the document as supplementary content—such as a standalone endnotes file, answer key, or glossary—that is meant to be reached via hyperlinks rather than encountered while turning pages sequentially. Note that any content document linked to from another content document must still be listed in the `<spine>`; setting `linear="no"` keeps it in the `<spine>` while signaling that it falls outside the main reading flow.

    > [!NOTE]
    > Reading systems vary in how they handle `linear="no"`. Some skip `linear="no"` items during normal page turns and only display them when the reader activates a link, while others place them at the end of the reading order or include them in the normal page-turning sequence.

- `id`
  - : An optional XML identifier for the `<itemref>` element itself.
- `properties`
  - : A space-separated list of spine property tokens. In reflowable EPUBs, the standard values are `page-spread-left` and `page-spread-right`, which instruct the reading system to place the first page of the referenced document on the left-hand (verso) or right-hand (recto) side when displaying a synthetic two-page spread.

## Examples

### Minimal spine

The following example shows a `<spine>` that orders a cover page, frontmatter, and two chapters by referencing the `id` values of `<item>` elements in the `<manifest>`:

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

### Spine with non-linear items and page-spread properties

The following example shows a `<spine>` with explicit left-to-right page progression (`page-progression-direction="ltr"`), a chapter forced to start on the right-hand page of a two-page spread (`properties="page-spread-right"`), and a standalone endnotes file marked with `linear="no"`:

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

- [Package file](/en-US/docs/Related/EPUB/Package)
- [EPUB package metadata](/en-US/docs/Related/EPUB/Package/Metadata)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
- [EPUB navigation](/en-US/docs/Related/EPUB/Navigation)
