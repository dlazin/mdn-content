---
title: EPUB SVG
short-title: SVG
slug: Related/EPUB/Reference/Content/SVG
page-type: guide
sidebar: epub
---

[Scalable Vector Graphics (SVG)](/en-US/docs/Web/SVG) (`image/svg+xml`) is a [core media type](/en-US/docs/Related/EPUB/Reference/Content/Core_media_types) in EPUB 3. Because vector graphics scale cleanly to any screen resolution, ebooks commonly use SVG for diagrams, maps, mathematical figures, publisher logos, decorative dividers, and full-screen cover pages.

## Ways to use SVG in EPUB

EPUB 3 supports three ways to include SVG in a book.

### Referenced from XHTML

You can embed an external SVG file inside an [XHTML](/en-US/docs/Related/EPUB/Reference/Content/XHTML) document using [`<img>`](/en-US/docs/Web/HTML/Reference/Elements/img) or [`<object>`](/en-US/docs/Web/HTML/Reference/Elements/object):

```xml
<figure>
  <img src="../images/pequod-rigging.svg" alt="Labeled diagram of the Pequod's three masts" />
  <figcaption>Rigging plan of a three-masted whaling ship.</figcaption>
</figure>
```

Declare both the SVG file (`media-type="image/svg+xml"`) and the XHTML file in the [manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest), and include `properties="svg"` on the XHTML manifest item.

### Inline SVG in XHTML

You can place an `<svg xmlns="http://www.w3.org/2000/svg" viewBox="...">` fragment directly inside an XHTML file. Inline SVG lets CSS rules (such as `fill: currentColor`) adapt line art automatically when a reader switches to sepia or dark mode. Any XHTML document containing inline SVG must also declare `properties="svg"` on its manifest item.

### Standalone SVG spine items

Alongside XHTML, `image/svg+xml` is one of the two native EPUB content document formats that can appear directly in the [spine](/en-US/docs/Related/EPUB/Reference/Package/Spine) without a fallback. Standalone SVG spine items are mainly used for fixed-layout graphic spreads, comics, and manga.

> [!NOTE]
> Even though standalone SVG files are valid in the spine, wrapping SVG inside an XHTML document (such as `cover.xhtml`) is usually more reliable in reflowable ebooks. Some reading systems handle pagination, zooming, and text selection more consistently on XHTML spine items than on standalone SVG spine items.

## EPUB restrictions on SVG

Whether an SVG graphic is standalone, referenced via `<img>`, or placed inline inside XHTML, EPUB 3 applies a few extra rules:

- SVG files must be well-formed XML and stored inside the EPUB container rather than loaded from remote URLs.
- If an SVG uses [`<foreignObject>`](/en-US/docs/Web/SVG/Reference/Element/foreignObject) to embed HTML markup, that markup must follow EPUB's [XHTML content document](/en-US/docs/Related/EPUB/Reference/Content/XHTML) rules. In a standalone or referenced SVG file, `<foreignObject>` must contain either HTML flow content or a single `<body>` element; in an inline SVG inside XHTML, `<body>` is not allowed inside `<foreignObject>`.
- Include an accessible `<title>` (and `<desc>` where helpful) in inline or standalone SVGs. Keep `<title>` plain text rather than nesting HTML elements inside it, because reading systems have limited support for child markup in SVG titles.
- Place [`epub:type`](/en-US/docs/Related/EPUB/Reference/Content/epub:type) only on renderable SVG elements. If an inline SVG inside XHTML uses custom vocabulary prefixes, declare `epub:prefix` on the root `<html>` element rather than on `<svg>`.

## Examples

### Inline SVG ornament that adapts to dark mode

Setting `fill="currentColor"` on an inline SVG lets a chapter ornament match the reader's active text color in light, sepia, and dark themes:

```xml
<div class="ornament" role="presentation" aria-hidden="true">
  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 120 20" width="120" height="20">
    <path fill="currentColor" d="M0 9h48l12-8 12 8h48v2H72l-12 8-12-8H0z" />
  </svg>
</div>
```

### Scalable cover wrapper using SVG inside XHTML

Wrapping a raster cover image in an `<svg>` element with `viewBox` set to the image's pixel dimensions scales the cover to fit any screen aspect ratio without cropping or spilling onto a blank second page:

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

If you need compatibility with older SVG 1.1 e-readers that do not recognize bare `href` on `<image>`, declare `xmlns:xlink="http://www.w3.org/1999/xlink"` on `<svg>` and use `xlink:href="../images/cover.jpg"`. Remember to set `properties="svg"` on the cover page and `properties="cover-image"` on the cover image in the [manifest](/en-US/docs/Related/EPUB/Reference/Content/Images#cover_images).

## Specifications

- [EPUB 3.3: SVG content documents](https://www.w3.org/TR/epub-33/#sec-svg)
- [EPUB 3.3: Embedded SVG in XHTML](https://www.w3.org/TR/epub-33/#sec-xhtml-svg)
- [EPUB 3.3: Restrictions on SVG](https://www.w3.org/TR/epub-33/#sec-svg-restrictions)

## See also

- [SVG: Scalable Vector Graphics](/en-US/docs/Web/SVG)
- [EPUB images](/en-US/docs/Related/EPUB/Reference/Content/Images)
- [EPUB XHTML content documents](/en-US/docs/Related/EPUB/Reference/Content/XHTML)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest)
