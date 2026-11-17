---
title: EPUB publication content
short-title: Overview
slug: Related/EPUB/Content
page-type: guide
sidebar: epub
---

While [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype), [`META-INF/container.xml`](/en-US/docs/Related/EPUB/Structure/META-INF), and the [package file](/en-US/docs/Related/EPUB/Package) (`.opf`) provide the packaging and metadata scaffolding for an ebook, **publication content** is what readers actually see, hear, and interact with. EPUB 3 builds its content layer directly on Open Web Platform standards—primarily [XHTML](/en-US/docs/Related/EPUB/Content/XHTML), [CSS](/en-US/docs/Related/EPUB/Content/CSS), [SVG](/en-US/docs/Related/EPUB/Content/SVG), [fonts](/en-US/docs/Related/EPUB/Content/Fonts), [images](/en-US/docs/Related/EPUB/Content/Images), [audio](/en-US/docs/Related/EPUB/Content/Audio), [video](/en-US/docs/Related/EPUB/Content/Video), and optional [JavaScript](/en-US/docs/Related/EPUB/Content/Scripting).

## Top-level content documents vs. embedded resources

EPUB distinguishes between documents that can appear as standalone entries in the reading order and assets used to render those documents:

- **EPUB content documents ([XHTML](/en-US/docs/Related/EPUB/Content/XHTML) and [SVG](/en-US/docs/Related/EPUB/Content/SVG))**: Only `application/xhtml+xml` and `image/svg+xml` documents can be referenced directly in the package [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine) without fallbacks. In a typical reflowable ebook, each chapter, frontmatter section, and backmatter section is an individual `.xhtml` file.
- **Supporting publication resources**: Stylesheets, fonts, raster images, audio clips, videos, captions, scripts, and SMIL Media Overlays are referenced from inside XHTML or SVG content documents rather than placed directly in `<spine>`.

Every publication resource—both top-level content documents and supporting assets—must be declared as an `<item>` in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest).

## Media types, fallbacks, and container storage

To keep ebooks portable across different reading systems and devices, EPUB enforces two resource rules that do not exist on the open web:

1. **[Core media types](/en-US/docs/Related/EPUB/Content/Core_media_types) and fallbacks**: Standard web formats designated as core media types (such as XHTML, SVG, CSS, PNG, JPEG, WebP, GIF, MP3, AAC MP4, Ogg Opus, and TrueType/OpenType/WOFF/WOFF2 fonts) work inside content documents without fallbacks. Non-core (*foreign*) formats require an HTML or manifest fallback unless they belong to an *exempt* category such as `<video>` or `<track>`.
2. **Self-contained offline packaging**: Almost all publication resources—including XHTML files, CSS stylesheets, and images—**must** be stored inside the `.epub` ZIP container so the book can be read offline without network access. Only audio, video, fonts, and data fetched by scripts are permitted to be hosted remotely over `https:`, and any manifest item referencing a remote resource must declare `properties="remote-resources"`.

## How EPUB rendering differs from web browsers

Although modern EPUB reading systems use browser rendering engines under the hood, reading an ebook differs from browsing a webpage in several important ways:

- **Dynamic pagination**: Instead of scrolling a single tall viewport, reflowable e-readers split each XHTML document into screen-sized pages (often using multi-column layout internally). Page boundaries shift whenever the reader changes font size, line spacing, or screen orientation.
- **Reader customization**: Users frequently override publisher fonts, margins, line heights, and color themes (such as sepia or dark mode). Well-authored EPUB content adapts to these preferences rather than fighting them with rigid pixel dimensions or hardcoded text colors.
- **Wide device range**: The same `.epub` file may be opened on a phone, tablet, desktop app, screen reader, or grayscale e-ink reader without speakers or JavaScript support.

## Publication content topics

- [XHTML](/en-US/docs/Related/EPUB/Content/XHTML)
  - : Authoring chapters in XML-serialized HTML, avoiding common XML syntax errors, and embedding [MathML](/en-US/docs/Web/MathML).
- [SVG](/en-US/docs/Related/EPUB/Content/SVG)
  - : Using vector graphics via `<img>`, inline `<svg>`, or standalone SVG spine documents, along with EPUB-specific SVG constraints.
- [CSS](/en-US/docs/Related/EPUB/Content/CSS)
  - : Styling reflowable text for paginated screens and dark mode, prohibited directional properties (`direction` and `unicode-bidi`), and legacy `-epub-` prefixes.
- [Fonts](/en-US/docs/Related/EPUB/Content/Fonts)
  - : Embedding WOFF2, WOFF, OpenType, and TrueType fonts with `@font-face`, and obfuscating licensed fonts via `META-INF/encryption.xml`.
- [Images](/en-US/docs/Related/EPUB/Content/Images)
  - : Supported image formats, accessible `alt` text and `<figure>` markup, responsive sizing across page breaks, and cover images.
- [Audio](/en-US/docs/Related/EPUB/Content/Audio)
  - : Embedding `<audio>` clips and synchronizing prerecorded read-aloud narration with SMIL Media Overlays.
- [Video](/en-US/docs/Related/EPUB/Content/Video)
  - : Embedding `<video>` clips, exempt codec considerations, captions via `<track>`, and fallbacks for e-ink devices.
- [Scripting](/en-US/docs/Related/EPUB/Content/Scripting)
  - : Container-constrained vs. spine-level JavaScript, declaring `properties="scripted"`, and progressive enhancement when scripting is disabled.
- [`epub:type` attribute](/en-US/docs/Related/EPUB/Content/epub:type)
  - : Adding EPUB structural semantics (such as pop-up footnotes) and pairing them with accessible ARIA `doc-*` roles.
- [Core media types](/en-US/docs/Related/EPUB/Content/Core_media_types)
  - : Complete reference of core, foreign, and exempt MIME media types and fallback rules.

## Specifications

- [EPUB 3.3: Publication resources](https://www.w3.org/TR/epub-33/#sec-publication-resources)
- [EPUB 3.3: EPUB content documents](https://www.w3.org/TR/epub-33/#sec-contentdocs)

## See also

- [Content directory](/en-US/docs/Related/EPUB/Structure/Content)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
- [EPUB package spine](/en-US/docs/Related/EPUB/Package/Spine)
