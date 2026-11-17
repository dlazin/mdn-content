---
title: EPUB SVG
short-title: SVG
slug: Related/EPUB/Content/SVG
page-type: guide
sidebar: epub
---

[Scalable Vector Graphics (SVG)](/en-US/docs/Web/SVG) (`image/svg+xml`) is a first-class format in EPUB 3. Because vector graphics scale cleanly to any screen resolution without blurring, SVG is widely used in ebooks for diagrams, maps, mathematical figures, publisher logos, decorative dividers, and responsive full-screen cover pages.

## Ways to use SVG in EPUB

EPUB 3 supports three ways to include SVG in a publication:

1. **Referenced from an [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) document**: Embed an external `.svg` file using [`<img>`](/en-US/docs/Web/HTML/Reference/Elements/img) or [`<object>`](/en-US/docs/Web/HTML/Reference/Elements/object):

   ```xml
   <figure>
     <img src="../images/pequod-rigging.svg" alt="Labeled diagram of the Pequod's three masts" />
     <figcaption>Rigging plan of a three-masted whaling ship.</figcaption>
   </figure>
   ```

   Both the `.svg` file (`media-type="image/svg+xml"`) and the `.xhtml` file must be declared in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest), and the `.xhtml` manifest `<item>` **must** declare `properties="svg"`.

2. **Inline `<svg>` inside an XHTML document**: Place an `<svg xmlns="http://www.w3.org/2000/svg" viewBox="...">` fragment directly inside an XHTML file. Inline SVG lets your book's CSS rules (such as `fill: currentColor`) adapt line art automatically when a reader switches to sepia or dark mode. Any XHTML document containing inline `<svg>` **must** also declare `properties="svg"` on its `<item>` in `<manifest>`:

   ```xml
   <item id="ch3" href="text/chapter-3.xhtml" media-type="application/xhtml+xml" properties="svg" />
   ```

3. **Standalone SVG content documents in `<spine>`**: Alongside XHTML, `image/svg+xml` is one of the two native *EPUB content document* formats that can be referenced directly by `<itemref>` in the package [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine) without a fallback. Standalone SVG spine items are primarily used for full-page cover wrappers, comics, manga, and fixed-layout graphic spreads.

> [!NOTE]
> Even though standalone `.svg` files are valid in `<spine>`, wrapping an `<svg>` element inside a standard `.xhtml` document (such as `cover.xhtml`) is often preferred in reflowable ebooks because some reading systems handle pagination, zooming, and text selection more consistently on XHTML spine items than on standalone SVG spine items.

## EPUB restrictions on SVG

Whether an SVG graphic is standalone, referenced via `<img>`, or placed inline inside XHTML, EPUB 3 applies several constraints to ensure consistent rendering across reading systems:

- **Conforming XML and local storage**: Like images and XHTML files, SVG files must be well-formed XML and must be stored inside the EPUB container rather than loaded from remote URLs.
- **Restrictions on `<foreignObject>`**: If an SVG uses `<foreignObject>` to embed HTML markup:
  - Its content must conform to EPUB's [XHTML content document](/en-US/docs/Related/EPUB/Content/XHTML) rules.
  - In a standalone or referenced `.svg` file, `<foreignObject>` must contain either HTML flow content or a single `<body>` element; in an inline `<svg>` inside XHTML, `<body>` is not allowed inside `<foreignObject>`.
- **Plain-text `<title>` elements**: Include an accessible `<title>` (and `<desc>` where helpful) inside inline or standalone SVGs, and keep `<title>` text-only rather than nesting HTML child elements inside it, as reading system support for marked-up SVG titles is limited.
- **`epub:type` and `epub:prefix`**: When using [`epub:type`](/en-US/docs/Related/EPUB/Content/epub:type) inside SVG, place it only on renderable SVG elements. If an inline `<svg>` inside an XHTML document uses custom vocabulary prefixes, declare `epub:prefix` on the root `<html>` element rather than on `<svg>`.

## Examples

### Inline SVG ornament that adapts to dark mode

Using `fill="currentColor"` on an inline SVG lets a chapter ornament automatically match the reader's active text color in light, sepia, or dark themes:

```xml
<div class="ornament" role="presentation" aria-hidden="true">
  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 120 20" width="120" height="20">
    <path fill="currentColor" d="M0 9h48l12-8 12 8h48v2H72l-12 8-12-8H0z" />
  </svg>
</div>
```

### Scalable cover wrapper using SVG inside XHTML

Wrapping a raster cover image inside an `<svg>` element with `viewBox` set to the image's pixel dimensions scales the cover cleanly to fit any screen aspect ratio without cropping or spilling onto a second blank page:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Cover</title>
    <style>
      html, body {
        margin: 0;
        padding: 0;
        height: 100%;
      }
      svg {
        display: block;
        width: 100%;
        height: 100%;
      }
    </style>
  </head>
  <body>
    <svg xmlns="http://www.w3.org/2000/svg"
         xmlns:xlink="http://www.w3.org/1999/xlink"
         viewBox="0 0 1600 2560"
         preserveAspectRatio="xMidYMid meet"
         role="img"
         aria-labelledby="cover-title">
      <title id="cover-title">Cover: Moby-Dick by Herman Melville</title>
      <image width="1600" height="2560" href="../images/cover.jpg" />
    </svg>
  </body>
</html>
```

Remember to declare `properties="svg"` on the `cover.xhtml` item and `properties="cover-image"` on `images/cover.jpg` in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest):

```xml
<manifest>
  <item id="cover-img" href="images/cover.jpg" media-type="image/jpeg" properties="cover-image" />
  <item id="cover-page" href="text/cover.xhtml" media-type="application/xhtml+xml" properties="svg" />
  <!-- other manifest items -->
</manifest>
```

## Specifications

- [EPUB 3.3: SVG content documents](https://www.w3.org/TR/epub-33/#sec-svg)
- [EPUB 3.3: Embedded SVG in XHTML](https://www.w3.org/TR/epub-33/#sec-xhtml-svg)
- [EPUB 3.3: Restrictions on SVG](https://www.w3.org/TR/epub-33/#sec-svg-restrictions)

## See also

- [SVG: Scalable Vector Graphics](/en-US/docs/Web/SVG)
- [EPUB images](/en-US/docs/Related/EPUB/Content/Images)
- [EPUB XHTML content documents](/en-US/docs/Related/EPUB/Content/XHTML)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
