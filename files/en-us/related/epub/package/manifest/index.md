---
title: EPUB package manifest
short-title: Manifest
slug: Related/EPUB/Package/Manifest
page-type: guide
sidebar: epub
---

The **`<manifest>`** element is the second required child of the `<package>` element in an EPUB [package file](/en-US/docs/Related/EPUB/Package) (`.opf`), appearing immediately after [`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata) and before [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine). It provides an exhaustive, unordered inventory of every publication resource included in the EPUB—such as XHTML content documents, the [navigation document](/en-US/docs/Related/EPUB/Navigation), CSS stylesheets, images, fonts, audio, video, and scripts.

Every publication resource in the [content directory](/en-US/docs/Related/EPUB/Structure/Content) must be declared as an `<item>` element inside `<manifest>`, and no unaccounted-for content files may be included in the container (aside from container-level files like [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype) and files in [`META-INF`](/en-US/docs/Related/EPUB/Structure/META-INF)). The order of `<item>` elements in `<manifest>` does not affect the reading order of the ebook; reading order is controlled by the [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).

## Attributes

The `<manifest>` element itself accepts one optional attribute:

- `id`
  - : An optional XML identifier for the `<manifest>` element.

## Child elements

Every `<manifest>` element must contain one or more **`<item>`** elements. Each `<item>` element is an empty (self-closing) XML element that declares a single resource in the publication:

```xml
<item id="chapter-1" href="text/chapter-1.xhtml" media-type="application/xhtml+xml" />
```

### Required `<item>` attributes

- `id`
  - : A unique XML identifier for the resource within the package file. Other parts of the package file—most notably [`<itemref>`](/en-US/docs/Related/EPUB/Package/Spine) elements in the `<spine>`, `fallback` chains, and `<meta refines="#...">` elements in [`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata)—reference the resource using this `id`.
- `href`
  - : The path (URL) to the resource. For files stored inside the EPUB container, this path is relative to the directory containing the `.opf` package file (not relative to the root of the EPUB container, unless the `.opf` file itself is in the root). Each `<item>` in `<manifest>` must have a unique `href`, and the `href` must not include a fragment identifier (`#...`).
- `media-type`
  - : The [MIME media type](/en-US/docs/Web/HTTP/Guides/MIME_types) indicating the format of the resource. Common media types in EPUB include:
    - `application/xhtml+xml` for XHTML content documents and the navigation document
    - `text/css` for CSS stylesheets
    - `image/jpeg`, `image/png`, `image/gif`, `image/webp`, and `image/svg+xml` for images
    - `font/ttf`, `font/otf`, `font/woff`, and `font/woff2` for fonts
    - `audio/mpeg`, `audio/mp4`, and `audio/ogg` for audio
    - `text/javascript` for scripts
    - `application/smil+xml` for Media Overlay documents

### Optional `<item>` attributes

- `properties`
  - : A space-separated list of property tokens that declare special roles or features of the resource (see [Manifest item properties](#manifest_item_properties) later on this page).
- `fallback`
  - : The `id` of another `<item>` in the manifest that serves as a fallback if a reading system does not support the current item's `media-type`. A fallback chain is required when an `<item>` with a non-core media type (see [Core media types](/en-US/docs/Related/EPUB/Content/Core_media_types)) is referenced directly in the [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine) or embedded without native HTML fallback mechanisms (such as `<picture>`, `<audio>`, `<video>`, or `<object>`).
- `media-overlay`
  - : The `id` of an `<item>` in the manifest that references a Media Overlay document (`application/smil+xml`) providing synchronized audio playback for this content document.

## Manifest item properties

The `properties` attribute on `<item>` uses a controlled vocabulary to inform reading systems about special roles or content features of a resource:

- `nav`
  - : Identifies the publication's [navigation document](/en-US/docs/Related/EPUB/Navigation). Exactly one `<item>` in the manifest must declare `properties="nav"`, and its `media-type` must be `application/xhtml+xml`.

    ```xml
    <item id="nav" href="nav.xhtml" media-type="application/xhtml+xml" properties="nav" />
    ```

- `cover-image`
  - : Identifies the primary raster or vector cover image for the publication, which reading systems and library views display on the bookshelf. At most one `<item>` in the manifest may declare `properties="cover-image"`.

    ```xml
    <item id="cover-img" href="images/cover.jpg" media-type="image/jpeg" properties="cover-image" />
    ```

- `mathml`
  - : Must be declared on any content document `<item>` that contains embedded [MathML](/en-US/docs/Web/MathML) markup.
- `svg`
  - : Must be declared on any XHTML content document `<item>` that contains embedded [SVG](/en-US/docs/Web/SVG) markup or references SVG images.
- `scripted`
  - : Must be declared on any content document `<item>` that includes JavaScript or HTML interactive forms.
- `remote-resources`
  - : Must be declared on any `<item>` (such as an XHTML document, CSS stylesheet, or Media Overlay) that references resources located outside the EPUB container via `http:` or `https:` URLs.
- `switch`
  - : Deprecated in EPUB 3. Previously indicated that the content document contained `<epub:switch>` elements.

If multiple properties apply to the same `<item>`, list them separated by spaces:

```xml
<item id="chapter-2" href="text/chapter-2.xhtml" media-type="application/xhtml+xml" properties="mathml svg" />
```

## Examples

### Minimal manifest

The following example shows a minimal `<manifest>` declaring the required navigation document (`properties="nav"`), a CSS stylesheet, and two XHTML chapter files:

```xml
<manifest>
  <item id="nav" href="nav.xhtml" media-type="application/xhtml+xml" properties="nav" />
  <item id="css" href="styles/book.css" media-type="text/css" />
  <item id="ch1" href="text/chapter-1.xhtml" media-type="application/xhtml+xml" />
  <item id="ch2" href="text/chapter-2.xhtml" media-type="application/xhtml+xml" />
</manifest>
```

### Manifest with cover image, fonts, and content properties

The following example shows a more complete `<manifest>` that includes a cover image (`properties="cover-image"`), a cover XHTML page, an embedded WOFF2 font, an illustration, and a chapter containing MathML and inline SVG (`properties="mathml svg"`):

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
