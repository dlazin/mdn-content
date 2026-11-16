---
title: EPUB core media types
short-title: Core media types
slug: Related/EPUB/Content/Core_media_types
page-type: guide
sidebar: epub
---

**Core media types** are the standard [MIME media types](/en-US/docs/Web/HTTP/Guides/MIME_types) that EPUB 3 reading systems are built to support without requiring fallback formats. When you use a core media type resource inside an EPUB content document—such as a JPEG or WebP illustration, an MP3 audio clip, a CSS stylesheet, or a WOFF2 font—you can declare it in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) and reference it directly.

Resources whose media types are not on the core media types list are classified as either **foreign resources** (which require an explicit fallback to a core media type) or **exempt resources** (such as video files, which are not guaranteed reading system support but do not require fallbacks).

## Core media types reference

The following table lists every core media type defined in EPUB 3.3, grouped by resource category. When multiple media types are listed for the same format, always use the **preferred `media-type`** in new EPUB 3 publications; legacy aliases are retained only for backward compatibility with older authoring tools.

| Category | Resource type | Preferred `media-type` | Legacy `media-type` aliases |
| --- | --- | --- | --- |
| **Content documents** | [XHTML](/en-US/docs/Glossary/XHTML) content documents | `application/xhtml+xml` | None |
| **Images** | GIF images | `image/gif` | None |
| **Images** | JPEG images | `image/jpeg` | None |
| **Images** | PNG images | `image/png` | None |
| **Images** | [SVG](/en-US/docs/Web/SVG) documents and images | `image/svg+xml` | None |
| **Images** | WebP images | `image/webp` | None |
| **Audio** | MP3 audio | `audio/mpeg` | None |
| **Audio** | AAC LC audio in MP4 container | `audio/mp4` | None |
| **Audio** | Opus audio in Ogg container | `audio/ogg; codecs=opus` | None |
| **Stylesheets** | [CSS](/en-US/docs/Web/CSS) stylesheets | `text/css` | None |
| **Fonts** | [TrueType fonts](/en-US/docs/Related/EPUB/Content/Fonts) (`.ttf`) | `font/ttf` | `application/font-sfnt` |
| **Fonts** | [OpenType fonts](/en-US/docs/Related/EPUB/Content/Fonts) (`.otf`) | `font/otf` | `application/font-sfnt`, `application/vnd.ms-opentype` |
| **Fonts** | [WOFF fonts](/en-US/docs/Related/EPUB/Content/Fonts) (`.woff`) | `font/woff` | `application/font-woff` |
| **Fonts** | [WOFF2 fonts](/en-US/docs/Related/EPUB/Content/Fonts) (`.woff2`) | `font/woff2` | None |
| **Scripts** | [JavaScript](/en-US/docs/Web/JavaScript) | `application/javascript` | `application/ecmascript`, `text/javascript` |
| **Synchronized audio** | SMIL Media Overlay documents | `application/smil+xml` | None |
| **Legacy navigation** | EPUB 2 NCX table of contents | `application/x-dtbncx+xml` | None |

> [!NOTE]
> Reading system support for core media types still depends on device capabilities—for example, an audio-only reading system without a visual screen does not render image or font resources, and e-ink devices without speakers cannot play `audio/mpeg` clips.

## Core media types in content documents vs. in the `<spine>`

A common source of confusion is assuming that any core media type can be placed directly in the package [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).

- **Inside content documents (no fallback needed)**: All core media types can be embedded or referenced inside an XHTML or SVG content document (for example, `<img src="images/cover.jpg" alt="Cover" />` or `<link rel="stylesheet" href="styles/main.css" />`) without fallbacks.
- **Directly in the `<spine>` (XHTML and SVG only)**: Only `application/xhtml+xml` and `image/svg+xml` are *EPUB content documents* that can be referenced directly from `<itemref>` elements in `<spine>` without fallbacks. Referencing any other core media type—such as a raw `image/jpeg` file—directly in `<spine>` treats it as a *foreign content document* and requires a manifest `fallback` chain resolving to an XHTML or SVG document. To display a raster image as its own page (such as a cover), wrap it inside an XHTML or SVG document instead.

## Foreign resources and fallbacks

Any resource whose MIME type is neither a core media type nor an [exempt resource](#exempt_resources)—for example, an AVIF image (`image/avif`) or a FLAC audio file (`audio/flac`)—is a **foreign resource**. Because reading systems are not guaranteed to render foreign resources, you must provide a fallback to a core media type using one of two mechanisms:

### HTML intrinsic fallbacks (recommended)

Whenever HTML provides native fallback elements, use them instead of package manifest fallbacks so the browser engine inside the reading system can select the first supported format automatically:

- **Images with [`<picture>`](/en-US/docs/Web/HTML/Reference/Elements/picture)**: Place foreign image formats in child [`<source>`](/en-US/docs/Web/HTML/Reference/Elements/source) elements with an explicit `type` attribute, and use a core media type (such as `image/jpeg` or `image/png`) on the required fallback `<img>` element:

  ```xml
  <picture>
    <source srcset="../images/chart.avif" type="image/avif" />
    <img src="../images/chart.png" alt="Annual whalebone production chart" />
  </picture>
  ```

  Remember to declare **both** `images/chart.avif` and `images/chart.png` in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest); when `<picture>` provides an intrinsic fallback to a core image format, the `.avif` manifest `<item>` does not need a `fallback` attribute.

- **Audio with [`<audio>`](/en-US/docs/Web/HTML/Reference/Elements/audio)**: List child `<source>` elements ending with at least one audio core media type (such as `audio/mpeg` or `audio/mp4`):

  ```xml
  <audio controls="controls">
    <source src="../audio/pronunciation.flac" type="audio/flac" />
    <source src="../audio/pronunciation.mp3" type="audio/mpeg" />
  </audio>
  ```

  Note that fallback prose placed inside `<audio>...</audio>` does **not** satisfy EPUB's fallback requirement for foreign audio formats, because modern reading systems recognize `<audio>` even when they do not support a particular audio codec—only a child `<source>` referencing an audio core media type counts as a fallback.

### Manifest fallback chains

When an element has no native fallback mechanism (or when a foreign content document is referenced directly in [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine)), you can set the `fallback` attribute on its `<item>` in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) to the `id` of another `<item>`, forming a chain that resolves to a core media type (or to an XHTML/SVG content document for `<spine>` items):

```xml
<manifest>
  <item id="diagram-pdf" href="docs/diagram.pdf" media-type="application/pdf" fallback="diagram-xhtml" />
  <item id="diagram-xhtml" href="text/diagram.xhtml" media-type="application/xhtml+xml" />
  <!-- other manifest items -->
</manifest>
```

> [!WARNING]
> Although manifest fallback chains satisfy EPUB validation rules, real-world reading system support for `fallback` attributes in `<manifest>` is limited and inconsistent. Prefer standard core media types or HTML intrinsic fallbacks (`<picture>`, `<audio>`, `<object>`) whenever possible.

## Exempt resources

EPUB 3 designates several resource categories as **exempt resources**. Like foreign resources, exempt resources are not guaranteed to work in every reading system; however, like core media types, they **do not require fallbacks**:

- **Video (`<video>`)**: EPUB 3 defines no core media types for video because browser and device ecosystems historically split across royalty-bearing and open codecs. Any video file referenced from [`<video>`](/en-US/docs/Web/HTML/Reference/Elements/video) (or its child `<source>` elements) is exempt from fallback requirements, though MP4 (H.264/AAC, `video/mp4`) and WebM (`video/webm`) are the most widely supported choices in practice.
- **Timed text tracks (`<track>`)**: Caption, subtitle, and description files (such as WebVTT, `text/vtt`) referenced from `<track>` elements inside `<audio>` or `<video>` are exempt so authors can supply accessible captions freely.
- **Non-core fonts**: Any [font](/en-US/docs/Related/EPUB/Content/Fonts) format outside the four font core media types is exempt because CSS `font-family` lists naturally fall back to system fonts.
- **HTML `<link>` targets and unembedded data files**: Non-core resources referenced solely via HTML `<link>` elements—or data files (such as JSON or CSV datasets) bundled in the container for scripts without being placed in `<spine>` or embedded via elements like `<img>` or `<object>`—are exempt from fallbacks.

## Specifications

- [EPUB 3.3: Core media types](https://www.w3.org/TR/epub-33/#sec-core-media-types)
- [EPUB 3.3: Publication resources](https://www.w3.org/TR/epub-33/#sec-publication-resources)
- [EPUB 3.3: Foreign resources](https://www.w3.org/TR/epub-33/#sec-foreign-resources)
- [EPUB 3.3: Exempt resources](https://www.w3.org/TR/epub-33/#sec-exempt-resources)
- [EPUB 3.3: Resource fallbacks](https://www.w3.org/TR/epub-33/#sec-resource-fallbacks)

## See also

- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
- [EPUB package spine](/en-US/docs/Related/EPUB/Package/Spine)
- [EPUB fonts](/en-US/docs/Related/EPUB/Content/Fonts)
- [Media types (MIME types)](/en-US/docs/Web/HTTP/Guides/MIME_types)
