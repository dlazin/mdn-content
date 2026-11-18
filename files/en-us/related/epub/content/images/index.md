---
title: EPUB images
short-title: Images
slug: Related/EPUB/Content/Images
page-type: guide
sidebar: epub
---

EPUB 3 supports standard web raster and vector image formats for illustrations, photographs, charts, ornaments, and book covers. Every image file must be stored inside the EPUB container (remote image URLs are not allowed) and declared as an `<item>` in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest).

## Supported image formats

EPUB 3 defines five image [core media types](/en-US/docs/Related/EPUB/Content/Core_media_types) that visual reading systems support without fallbacks:

| Format                                               | `media-type` in `<manifest>` | Typical use cases                                                                         |
| ---------------------------------------------------- | ---------------------------- | ----------------------------------------------------------------------------------------- |
| JPEG (`.jpg`, `.jpeg`)                               | `image/jpeg`                 | Photographs, painted illustrations, and full-color cover art                              |
| PNG (`.png`)                                         | `image/png`                  | Screenshots, graphics with transparency, and sharp edges                                  |
| WebP (`.webp`)                                       | `image/webp`                 | Lossy or lossless photographs and illustrations at smaller file sizes (added in EPUB 3.3) |
| GIF (`.gif`)                                         | `image/gif`                  | Simple legacy graphics (prefer PNG or SVG for static artwork)                             |
| [SVG](/en-US/docs/Related/EPUB/Content/SVG) (`.svg`) | `image/svg+xml`              | Line art, diagrams, maps, logos, and scalable ornaments                                   |

Because `image/webp` was added in EPUB 3.3, older EPUB 3 e-readers and some retailer cover-ingestion tools may not render WebP images. JPEG and PNG remain the safest choices for cover thumbnails and maximum device compatibility.

> [!NOTE]
> Whenever an XHTML document references an `.svg` image—even via `<img src="...">`—you must add `properties="svg"` to that XHTML document's `<item>` in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest).

### Non-core image formats (`<picture>`)

Formats such as AVIF (`image/avif`) are *foreign resources* in EPUB 3. You can include them with an HTML [`<picture>`](/en-US/docs/Web/HTML/Reference/Elements/picture) element by setting `type` on [`<source>`](/en-US/docs/Web/HTML/Reference/Elements/source), providing a core media type fallback on [`<img>`](/en-US/docs/Web/HTML/Reference/Elements/img), and declaring both image files in `<manifest>`:

```xml
<picture>
  <source srcset="../images/whale-anatomy.avif" type="image/avif" />
  <img src="../images/whale-anatomy.jpg" alt="Cutaway illustration of a sperm whale's head" />
</picture>
```

## Markup and accessibility

Embed images in [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) content documents with self-closed `<img ... />` elements, wrapped in [`<figure>`](/en-US/docs/Web/HTML/Reference/Elements/figure) and [`<figcaption>`](/en-US/docs/Web/HTML/Reference/Elements/figcaption) when accompanied by a caption:

```xml
<figure>
  <img src="../images/nantucket-harbor.jpg"
       alt="Nineteenth-century engraving of whaling ships anchored in Nantucket harbor" />
  <figcaption>Whaling vessels fitting out at Nantucket.</figcaption>
</figure>
```

- Write concise `alt` text for every image that conveys information. Describe what the image shows rather than repeating a visible `<figcaption>` word for word.
- Use `alt=""` (optionally with `role="presentation"`) on purely decorative dividers or flourishes so screen readers skip them.
- Check transparent PNGs and SVGs in dark mode: pure black line art (`#000000`) on a transparent background disappears against dark reading themes. Add a light background or halo behind dark ink, or use [inline SVG](/en-US/docs/Related/EPUB/Content/SVG) with `currentColor`.

## Sizing images for paginated screens

Unlike scrolling webpages, reflowable reading systems fit content into fixed-height screen pages. Oversized images without responsive sizing can overflow the viewport or split awkwardly:

- Set `max-width: 100%; height: auto;` in CSS so wide images shrink proportionally on small screens without distorting their aspect ratio.
- Set `break-inside: avoid;` on `figure` elements so reading systems keep an image and its `<figcaption>` on the same page whenever they fit.

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
