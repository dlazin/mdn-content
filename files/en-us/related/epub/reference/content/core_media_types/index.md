---
title: EPUB core media types
short-title: Core media types
slug: Related/EPUB/Reference/Content/Core_media_types
page-type: guide
sidebar: epub
---

Core media types are the standard [MIME media types](/en-US/docs/Web/HTTP/Guides/MIME_types) that EPUB 3 reading systems support without requiring fallback formats. When you use a core media type inside a content document—such as a JPEG image, an MP3 audio clip, a CSS stylesheet, or a WOFF2 font—you can declare it in the [manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest) and reference it directly.

Resources outside the core media types list are either [foreign resources](#foreign_resources_and_fallbacks) (which require a fallback to a core media type) or [exempt resources](#exempt_resources) (such as video files, which are not guaranteed reading-system support and do not require fallbacks).

## Core media types reference

The table lists all core media types defined in EPUB 3.3. Where legacy aliases exist, use the preferred `media-type` in new EPUB 3 files.

| Category           | Resource type                                                                    | Preferred `media-type`     | Legacy `media-type` aliases                            |
| ------------------ | -------------------------------------------------------------------------------- | -------------------------- | ------------------------------------------------------ |
| Content documents  | [XHTML](/en-US/docs/Related/EPUB/Reference/Content/XHTML) content documents      | `application/xhtml+xml`    | None                                                   |
| Images             | GIF images                                                                       | `image/gif`                | None                                                   |
| Images             | JPEG images                                                                      | `image/jpeg`               | None                                                   |
| Images             | PNG images                                                                       | `image/png`                | None                                                   |
| Images             | [SVG](/en-US/docs/Related/EPUB/Reference/Content/SVG) documents and images       | `image/svg+xml`            | None                                                   |
| Images             | WebP images                                                                      | `image/webp`               | None                                                   |
| Audio              | MP3 audio                                                                        | `audio/mpeg`               | None                                                   |
| Audio              | AAC LC audio in MP4 container                                                    | `audio/mp4`                | None                                                   |
| Audio              | Opus audio in Ogg container                                                      | `audio/ogg; codecs=opus`   | None                                                   |
| Stylesheets        | [CSS](/en-US/docs/Related/EPUB/Reference/Content/CSS) stylesheets                | `text/css`                 | None                                                   |
| Fonts              | [TrueType fonts](/en-US/docs/Related/EPUB/Reference/Content/Fonts) (`.ttf`)      | `font/ttf`                 | `application/font-sfnt`                                |
| Fonts              | [OpenType fonts](/en-US/docs/Related/EPUB/Reference/Content/Fonts) (`.otf`)      | `font/otf`                 | `application/font-sfnt`, `application/vnd.ms-opentype` |
| Fonts              | [WOFF fonts](/en-US/docs/Related/EPUB/Reference/Content/Fonts) (`.woff`)         | `font/woff`                | `application/font-woff`                                |
| Fonts              | [WOFF2 fonts](/en-US/docs/Related/EPUB/Reference/Content/Fonts) (`.woff2`)       | `font/woff2`               | None                                                   |
| Scripts            | [JavaScript](/en-US/docs/Related/EPUB/Reference/Content/Scripting)               | `application/javascript`   | `application/ecmascript`, `text/javascript`            |
| Synchronized audio | [SMIL Media Overlay](/en-US/docs/Related/EPUB/Reference/Content/Audio) documents | `application/smil+xml`     | None                                                   |
| Legacy navigation  | EPUB 2 NCX table of contents                                                     | `application/x-dtbncx+xml` | None                                                   |

> [!NOTE]
> Support for core media types still depends on device capabilities and software versions. Audio-only reading systems do not render images or fonts, speakerless e-ink devices cannot play audio clips, and older e-readers and retailer ingestion systems often reject WebP (`image/webp`)—avoid using `image/webp` for `properties="cover-image"`, and prefer JPEG or PNG instead.

## Content documents vs. spine items

Not every core media type can be placed directly in the [spine](/en-US/docs/Related/EPUB/Reference/Package/Spine):

- Inside XHTML or SVG content documents, you can embed or link any core media type (for example, `<img src="../images/cover.jpg" alt="Cover" />` or `<link rel="stylesheet" href="../styles/main.css" />`) without fallbacks.
- Directly in the spine, only `application/xhtml+xml` and `image/svg+xml` work without fallbacks. Placing any other core media type—such as a raw JPEG image—in the spine treats it as a foreign content document and requires a manifest `fallback` chain ending at an XHTML or SVG document. To show a raster image as its own page, wrap it in an XHTML or SVG document instead.

## Foreign resources and fallbacks

Any resource whose MIME type is neither a core media type nor an [exempt resource](#exempt_resources)—such as an AVIF image (`image/avif`) or a FLAC audio clip (`audio/flac`)—is a foreign resource and must fall back to a core media type.

### HTML intrinsic fallbacks (recommended)

Where HTML provides native fallback elements, use them instead of manifest fallbacks so the reading system's browser engine can pick a supported format automatically:

- For images, use [`<picture>`](/en-US/docs/Web/HTML/Reference/Elements/picture) with `type` on [`<source>`](/en-US/docs/Web/HTML/Reference/Elements/source) and a core image format on the fallback `<img>` element, declaring both files in the manifest:

  ```xml
  <picture>
    <source srcset="../images/chart.avif" type="image/avif" />
    <img src="../images/chart.png" alt="Annual whalebone production chart" />
  </picture>
  ```

- For audio, list child `<source>` elements inside [`<audio>`](/en-US/docs/Web/HTML/Reference/Elements/audio) ending with an audio core media type:

  ```xml
  <audio controls="controls">
    <source src="../audio/pronunciation.flac" type="audio/flac" />
    <source src="../audio/pronunciation.mp3" type="audio/mpeg" />
  </audio>
  ```

  Text inside `<audio>...</audio>` does not count as an EPUB fallback for foreign audio codecs, because reading systems recognize `<audio>` even when a codec is unsupported.

### Manifest fallback chains

When an element has no built-in HTML fallback (or when a foreign document appears in the [spine](/en-US/docs/Related/EPUB/Reference/Package/Spine)), set `fallback` on its `<item>` in the [manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest) to the `id` of another item that resolves to a core media type (or to XHTML or SVG for spine items):

```xml
<manifest>
  <item id="diagram-pdf" href="docs/diagram.pdf" media-type="application/pdf" fallback="diagram-xhtml" />
  <item id="diagram-xhtml" href="text/diagram.xhtml" media-type="application/xhtml+xml" />
  <!-- other manifest items -->
</manifest>
```

> [!WARNING]
> Although manifest `fallback` chains pass EPUB validation, reading-system support for them is inconsistent. Prefer core media types or HTML fallbacks (`<picture>`, `<audio>`, `<object>`) whenever possible.

## Exempt resources

EPUB 3 exempts four categories of non-core resources from fallback requirements, even though reading systems are not guaranteed to support them:

- [Video](/en-US/docs/Related/EPUB/Reference/Content/Video) files referenced from [`<video>`](/en-US/docs/Web/HTML/Reference/Elements/video) or its `<source>` children (`video/mp4` and `video/webm` are the most widely supported choices).
- Timed text tracks (such as WebVTT `text/vtt`) referenced from `<track>` inside `<audio>` or `<video>`.
- Non-core [font](/en-US/docs/Related/EPUB/Reference/Content/Fonts) formats, because CSS `font-family` stacks fall back to system fonts.
- Non-core targets of HTML `<link>` elements, plus unembedded data files (such as JSON or CSV files loaded by scripts) that are neither placed in the spine nor embedded with elements like `<img>` or `<object>`.

## Specifications

- [EPUB 3.3: Core media types](https://www.w3.org/TR/epub-33/#sec-core-media-types)
- [EPUB 3.3: Publication resources](https://www.w3.org/TR/epub-33/#sec-publication-resources)
- [EPUB 3.3: Foreign resources](https://www.w3.org/TR/epub-33/#sec-foreign-resources)
- [EPUB 3.3: Exempt resources](https://www.w3.org/TR/epub-33/#sec-exempt-resources)
- [EPUB 3.3: Resource fallbacks](https://www.w3.org/TR/epub-33/#sec-resource-fallbacks)
