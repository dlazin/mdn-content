---
title: EPUB images
short-title: Images
slug: Related/EPUB/Content/Images
page-type: guide
sidebar: epub
---

EPUB 3 supports standard web raster and vector image formats for illustrations, photographs, charts, decorative ornaments, and book covers. Every image file used in the publication **must** be stored inside the EPUB container (remote image hosting is not allowed) and declared as an `<item>` in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest).

## Supported image formats

EPUB 3 defines five **image [core media types](/en-US/docs/Related/EPUB/Content/Core_media_types)** that visual reading systems support without requiring fallback formats:

| Format | `media-type` in `<manifest>` | Typical use cases |
| --- | --- | --- |
| JPEG (`.jpg`, `.jpeg`) | `image/jpeg` | Photographs, painted illustrations, and full-color cover art |
| PNG (`.png`) | `image/png` | Screenshots, graphics requiring alpha transparency, and sharp contrast edges |
| WebP (`.webp`) | `image/webp` | Lossy or lossless photographs and illustrations with smaller file sizes (added as a core media type in EPUB 3.3) |
| GIF (`.gif`) | `image/gif` | Simple legacy graphics (prefer PNG or SVG for static artwork) |
| [SVG](/en-US/docs/Related/EPUB/Content/SVG) (`.svg`) | `image/svg+xml` | Line art, diagrams, maps, logos, and scalable ornaments |

> [!NOTE]
> Whenever an XHTML document references an `.svg` image (even via `<img src="...">`), you must include `properties="svg"` on that XHTML document's `<item>` in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest).

### Using non-core image formats (`<picture>`)

Newer formats such as AVIF (`image/avif`) or HEIC (`image/heic`) are classified as *foreign resources* in EPUB 3. You can include them by wrapping them in an HTML [`<picture>`](/en-US/docs/Web/HTML/Reference/Elements/picture) element with an explicit `type` attribute on [`<source>`](/en-US/docs/Web/HTML/Reference/Elements/source) and a core media type fallback on [`<img>`](/en-US/docs/Web/HTML/Reference/Elements/img), declaring both image files in `<manifest>`:

```xml
<picture>
  <source srcset="../images/whale-anatomy.avif" type="image/avif" />
  <img src="../images/whale-anatomy.jpg" alt="Cutaway illustration of a sperm whale's head" />
</picture>
```

## Markup and accessibility

Embed images inside [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) content documents using self-closed `<img ... />` elements, wrapped in [`<figure>`](/en-US/docs/Web/HTML/Reference/Elements/figure) and [`<figcaption>`](/en-US/docs/Web/HTML/Reference/Elements/figcaption) when accompanied by a caption:

```xml
<figure>
  <img src="../images/nantucket-harbor.jpg"
       alt="Nineteenth-century engraving of whaling ships anchored in Nantucket harbor" />
  <figcaption>Whaling vessels fitting out at Nantucket.</figcaption>
</figure>
```

- **Meaningful `alt` text**: Provide concise, descriptive `alt` text for every image that conveys information. Do not repeat the visible `<figcaption>` verbatim inside `alt`—describe what the image shows while `<figcaption>` provides the editorial caption.
- **Decorative images**: For purely decorative dividers or flourishes that convey no content meaning, use an empty `alt=""` attribute (optionally paired with `role="presentation"`) so screen readers skip them cleanly.
- **Dark-mode contrast**: Transparent PNGs or SVGs drawn with pure black strokes (`#000000`) disappear when a reader switches to a dark/night reading theme. For transparent diagrams or wordmarks, either add a subtle light pad/halo behind dark ink or use [inline SVG](/en-US/docs/Related/EPUB/Content/SVG) with `currentColor`.

## Sizing images for paginated screens

Unlike scrolling webpages, reflowable reading systems fit content into fixed-height screen pages. Large images without responsive sizing can overflow the page edge or clip across two pages:

- Set `max-width: 100%; height: auto;` in CSS so wide images shrink proportionally on narrow phone screens without distorting their aspect ratio.
- Add `break-inside: avoid;` on `figure` elements so reading systems keep an image and its `<figcaption>` together on the same page whenever they fit.

## Cover images

In EPUB 3, a book's cover is typically set up in two complementary places:

1. **Bookshelf thumbnail (`properties="cover-image"`)**: Declare `properties="cover-image"` on the raster or SVG cover image `<item>` in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest). Reading systems use this asset to display your book's cover in the user's library view—even before the book is opened. At most one `<item>` in `<manifest>` may have `properties="cover-image"`.
2. **First page in the reading order (`cover.xhtml`)**: Because raw JPEG, PNG, or WebP files cannot be placed directly in [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine) without fallbacks, wrap the cover image inside an XHTML document (or [SVG wrapper](/en-US/docs/Related/EPUB/Content/SVG#scalable_cover_wrapper_using_svg_inside_xhtml)) and list that document as the first `<itemref>` in `<spine>`:

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
