---
title: EPUB package manifest
short-title: Manifest
slug: Related/EPUB/Package/Manifest
page-type: guide
sidebar: epub
---

The `<manifest>` element is the second child of `<package>` in an EPUB [package file](/en-US/docs/Related/EPUB/Package) (`.opf`), placed after [`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata) and before [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine). It lists every publication resource used to render the book, including XHTML chapters, the [navigation document](/en-US/docs/Related/EPUB/Navigation), CSS stylesheets, images, fonts, audio, video, and scripts.

Every publication resource in the [content directory](/en-US/docs/Related/EPUB/Structure/Content) (as well as any remote audio, video, or font resource) must have an `<item>` entry in `<manifest>`. Container files ([`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype) and files in [`META-INF/`](/en-US/docs/Related/EPUB/Structure/META-INF)), the `.opf` file itself, and standalone metadata records linked from `<metadata>` must not appear in `<manifest>`.

The order of `<item>` elements in `<manifest>` does not affect chapter order; reading order is set by the [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).

## Attributes

`<manifest>` accepts one optional attribute:

- `id`
  - : An XML identifier for the `<manifest>` element.

## The `<item>` element

`<manifest>` contains one or more self-closing `<item>` elements, each declaring a single resource:

```xml
<item id="chapter-1" href="text/chapter-1.xhtml" media-type="application/xhtml+xml" />
```

### `<item>` attributes

- `id`
  - : Required. A unique XML identifier within the package file, referenced by [`<itemref>`](/en-US/docs/Related/EPUB/Package/Spine) elements in `<spine>`, `fallback` chains, `media-overlay` attributes, and `<meta refines="#...">` elements.
- `href`
  - : Required. The URL of the resource. For files inside the EPUB archive, write the path relative to the folder containing the `.opf` file (not relative to the archive root, unless `.opf` is in the root). Every `<item>` must resolve to a unique URL and must not include a `#` fragment identifier or a `data:` or `file:` URL.
- `media-type`
  - : Required. The [MIME media type](/en-US/docs/Web/HTTP/Guides/MIME_types) of the resource. Common EPUB [core media types](/en-US/docs/Related/EPUB/Content/Core_media_types) include:
    - `application/xhtml+xml` for XHTML content documents and the navigation document
    - `text/css` for CSS stylesheets
    - `image/jpeg`, `image/png`, `image/webp`, `image/gif`, and `image/svg+xml` for images
    - `font/woff2`, `font/woff`, `font/otf`, and `font/ttf` for fonts
    - `audio/mpeg`, `audio/mp4`, and `audio/ogg; codecs=opus` for audio
    - `application/javascript` (or `text/javascript`) for scripts
    - `application/smil+xml` for Media Overlay documents
- `properties`
  - : Optional. Space-separated tokens declaring special roles or content features of the resource (see [Manifest item properties](#manifest_item_properties)).
- `fallback`
  - : Optional (conditionally required). The `id` of another `<item>` in `<manifest>` to use if the reading system cannot render this item's `media-type`. Required when a non-core resource is referenced without a built-in HTML fallback (such as `<picture>`, `<audio>`, or `<object>`), or when anything other than XHTML or SVG is placed in `<spine>`.
- `media-overlay`
  - : Optional. The `id` of a Media Overlay `<item>` (`application/smil+xml`) providing synchronized audio playback for this content document.

## Manifest item properties

The `properties` attribute on `<item>` tells reading systems which files serve special roles or use features that require extra processing:

- `nav`
  - : Identifies the book's [navigation document](/en-US/docs/Related/EPUB/Navigation). Exactly one `<item>` in `<manifest>` must declare `properties="nav"`, and its `media-type` must be `application/xhtml+xml`.

    ```xml
    <item id="nav" href="nav.xhtml" media-type="application/xhtml+xml" properties="nav" />
    ```

- `cover-image`
  - : Identifies the cover image displayed on the reading app's bookshelf. Optional; allowed on at most one `<item>`.

    ```xml
    <item id="cover-img" href="images/cover.jpg" media-type="image/jpeg" properties="cover-image" />
    ```

- `mathml`
  - : Required on any content document `<item>` that contains [MathML](/en-US/docs/Web/MathML) markup.
- `svg`
  - : Required on any XHTML `<item>` that embeds inline `<svg>` markup or references external `.svg` files. Do not set `properties="svg"` on standalone `image/svg+xml` items themselves.
- `scripted`
  - : Required on any content document `<item>` that includes executable `<script>` elements or HTML `<form>` elements.
- `remote-resources`
  - : Required on any `<item>` (such as an XHTML document, CSS stylesheet, or Media Overlay) that loads audio, video, fonts, or script data from external URLs. Ordinary `<a href="https://...">` hyperlinks do not trigger `remote-resources`.
- `switch`
  - : Deprecated. Previously indicated deprecated `<epub:switch>` markup.

Separate multiple property tokens with spaces:

```xml
<item id="chapter-2" href="text/chapter-2.xhtml" media-type="application/xhtml+xml" properties="mathml svg" />
```

Item properties are not inherited across `<iframe>` boundaries: if an unscripted chapter embeds a scripted `<iframe>` document, only the embedded document's `<item>` declares `properties="scripted"`.

## Examples

### Minimal manifest

```xml
<manifest>
  <item id="nav" href="nav.xhtml" media-type="application/xhtml+xml" properties="nav" />
  <item id="css" href="styles/book.css" media-type="text/css" />
  <item id="ch1" href="text/chapter-1.xhtml" media-type="application/xhtml+xml" />
  <item id="ch2" href="text/chapter-2.xhtml" media-type="application/xhtml+xml" />
</manifest>
```

### Manifest with cover image, font, and feature properties

```xml
<manifest>
  <item id="nav" href="nav.xhtml" media-type="application/xhtml+xml" properties="nav" />
  <item id="cover-img" href="images/cover.jpg" media-type="image/jpeg" properties="cover-image" />
  <item id="cover-page" href="text/cover.xhtml" media-type="application/xhtml+xml" />
  <item id="css" href="styles/main.css" media-type="text/css" />
  <item id="body-font" href="fonts/literata-regular.woff2" media-type="font/woff2" />
  <item id="map-fig" href="images/nantucket-map.png" media-type="image/png" />
  <item id="ch1" href="text/chapter-1.xhtml" media-type="application/xhtml+xml" />
  <item id="ch2" href="text/chapter-2.xhtml" media-type="application/xhtml+xml" properties="mathml svg" />
</manifest>
```

## Specifications

- [EPUB 3.3: The `manifest` element](https://www.w3.org/TR/epub-33/#sec-manifest-elem)
- [EPUB 3.3: The `item` element](https://www.w3.org/TR/epub-33/#sec-item-elem)
- [EPUB 3.3: Manifest properties vocabulary](https://www.w3.org/TR/epub-33/#app-item-properties-vocab)
- [EPUB 3.3: Core media types](https://www.w3.org/TR/epub-33/#sec-core-media-types)

## See also

- [EPUB package file](/en-US/docs/Related/EPUB/Package)
- [EPUB package metadata](/en-US/docs/Related/EPUB/Package/Metadata)
- [EPUB package spine](/en-US/docs/Related/EPUB/Package/Spine)
- [EPUB core media types](/en-US/docs/Related/EPUB/Content/Core_media_types)
- [EPUB navigation document](/en-US/docs/Related/EPUB/Navigation)
