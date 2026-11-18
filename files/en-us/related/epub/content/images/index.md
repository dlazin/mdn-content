---
title: EPUB images
short-title: Images
slug: Related/EPUB/Content/Images
page-type: guide
sidebar: epub
---

EPUB 3 supports standard web raster and vector image formats for illustrations, photographs, charts, ornaments, and book covers. Every image file must be stored inside the EPUB container (remote image URLs are not allowed) and declared as an `<item>` in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest).

## Supported image formats

EPUB 3 defines five image [core media types](/en-US/docs/Related/EPUB/Content/Core_media_types):

| Format                                           | `media-type` in `<manifest>` | Typical use cases                                                                        |
| ------------------------------------------------ | ---------------------------- | ---------------------------------------------------------------------------------------- |
| JPEG (`.jpg`, `.jpeg`)                           | `image/jpeg`                 | Photographs, painted illustrations, and full-color cover art                             |
| PNG (`.png`)                                     | `image/png`                  | Screenshots, graphics with transparency, and sharp edges                                 |
| WebP (`.webp`)                                   | `image/webp`                 | Lossy or lossless photographs and illustrations at smaller file sizes (added in EPUB 3.3) |
| GIF (`.gif`)                                     | `image/gif`                  | Simple legacy graphics (prefer PNG or SVG for static artwork)                            |
| [SVG](/en-US/docs/Related/EPUB/Content/SVG) (`.svg`) | `image/svg+xml`              | Line art, diagrams, maps, logos, and scalable ornaments                                  |

Because `image/webp` was added in EPUB 3.3, older EPUB 3 e-readers and some retailer cover-ingestion tools may not render WebP images. JPEG and PNG remain the safest choices for cover thumbnails and maximum device compatibility. For non-core formats such as AVIF (`image/avif`), provide a [`<picture>` fallback](/en-US/docs/Related/EPUB/Content/Core_media_types#html_intrinsic_fallbacks_recommended) to a core image format.

> [!NOTE]
> Whenever an XHTML document references an `.svg` image—even via `<img src="...">`—you must add `properties="svg"` to that XHTML document's `<item>` in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest).

## Markup, accessibility, and sizing

Embed images in [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) content documents with self-closed `<img ... />` elements, wrapped in [`<figure>`](/en-US/docs/Web/HTML/Reference/Elements/figure) and [`<figcaption>`](/en-US/docs/Web/HTML/Reference/Elements/figcaption) when accompanied by a caption:

```xml
<figure>
  <img src="../images/nantucket-harbor.jpg"
       alt="Nineteenth-century engraving of whaling ships anchored in Nantucket harbor" />
  <figcaption>Whaling vessels fitting out at Nantucket.</figcaption>
</figure>
```

- Write concise `alt` text for every image that conveys information, describing what the image shows rather than repeating `<figcaption>` verbatim. Use `alt=""` (optionally with `role="presentation"`) on purely decorative dividers or flourishes.
- Check transparent PNGs and SVGs in dark mode: pure black line art (`#000000`) on a transparent background disappears against dark reading themes. Add a light background or halo behind dark ink, or use [inline SVG](/en-US/docs/Related/EPUB/Content/SVG) with `currentColor`.
- Size images responsively in [CSS](/en-US/docs/Related/EPUB/Content/CSS) (`max-width: 100%; height: auto;`) and set `break-inside: avoid;` on `figure` elements so paginated screens keep an image and its `<figcaption>` together whenever they fit.

## Cover images

Most EPUB 3 books declare their cover in two places:

- Add `properties="cover-image"` to the cover image `<item>` in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) (allowed on at most one `<item>`). Reading apps use this file to show the cover thumbnail on the user's bookshelf before the book is opened.
- Wrap the cover image inside an XHTML document (such as `cover.xhtml`, or an [SVG cover wrapper](/en-US/docs/Related/EPUB/Content/SVG#scalable_cover_wrapper_using_svg_inside_xhtml)) and list that document first in [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine), because raw JPEG, PNG, or WebP files cannot be placed directly in `<spine>` without fallbacks. When using `<img>` in `cover.xhtml`, put `role="doc-cover"` and descriptive `alt` text on the `<img>` element itself (not on `<section>` or `<body>`):

```xml
<!-- In text/cover.xhtml -->
<img src="../images/cover.jpg" role="doc-cover" alt="Cover: Moby-Dick by Herman Melville" />
```

```xml
<!-- In package.opf -->
<manifest>
  <item id="cover-img" href="images/cover.jpg" media-type="image/jpeg" properties="cover-image" />
  <item id="cover-page" href="text/cover.xhtml" media-type="application/xhtml+xml" />
  <!-- other manifest items -->
</manifest>
<spine>
  <itemref idref="cover-page" />
  <!-- remaining spine items -->
</spine>
```

## Specifications

- [EPUB 3.3: Core media types — Images](https://www.w3.org/TR/epub-33/#cmt-grp-image)
- [EPUB 3.3: HTML `img` fallbacks](https://www.w3.org/TR/epub-33/#sec-fallbacks-img)
- [EPUB 3.3: Manifest `cover-image` property](https://www.w3.org/TR/epub-33/#sec-cover-image)

## See also

- [`<img>`: The Image Embed element](/en-US/docs/Web/HTML/Reference/Elements/img)
- [`<picture>`: The Picture element](/en-US/docs/Web/HTML/Reference/Elements/picture)
- [EPUB SVG](/en-US/docs/Related/EPUB/Content/SVG)
- [EPUB core media types](/en-US/docs/Related/EPUB/Content/Core_media_types)
