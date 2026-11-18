---
title: EPUB publication content
short-title: Overview
slug: Related/EPUB/Content
page-type: guide
sidebar: epub
---

While [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype), [`META-INF/container.xml`](/en-US/docs/Related/EPUB/Structure/META-INF), and the [package file](/en-US/docs/Related/EPUB/Package) handle packaging and metadata, **publication content** is what readers see, hear, and interact with.

## Content documents vs. supporting resources

EPUB separates top-level documents in the reading order from the assets those documents reference:

- Only [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) (`application/xhtml+xml`) and [SVG](/en-US/docs/Related/EPUB/Content/SVG) (`image/svg+xml`) are EPUB content documents that can appear directly in the package [spine](/en-US/docs/Related/EPUB/Package/Spine) without fallbacks. In most reflowable books, each chapter or frontmatter section is its own XHTML file.
- Supporting assets—stylesheets, fonts, raster images, audio, video, caption tracks, scripts, and Media Overlays—are referenced from inside XHTML or SVG documents rather than placed in the spine.

Both content documents and supporting assets must be listed in the [manifest](/en-US/docs/Related/EPUB/Package/Manifest). For media format rules and how ebook rendering differs from web browsers, see [Core media types](/en-US/docs/Related/EPUB/Content/Core_media_types) and [EPUB's relationship to the web](/en-US/docs/Related/EPUB/Relationship_to_the_web).

## Publication content topics

- [XHTML](/en-US/docs/Related/EPUB/Content/XHTML)
  - : Authoring chapters in XML-syntax HTML, avoiding XML parsing errors, and embedding [MathML](/en-US/docs/Web/MathML).
- [SVG](/en-US/docs/Related/EPUB/Content/SVG)
  - : Including vector graphics via `<img>`, inline SVG, or standalone SVG spine pages.
- [CSS](/en-US/docs/Related/EPUB/Content/CSS)
  - : Styling reflowable text for paginated screens and dark mode, prohibited `direction` and `unicode-bidi` properties, and legacy `-epub-` prefixes.
- [Fonts](/en-US/docs/Related/EPUB/Content/Fonts)
  - : Embedding WOFF2, WOFF, OpenType, and TrueType fonts with `@font-face`, and obfuscating licensed fonts via `META-INF/encryption.xml`.
- [Images](/en-US/docs/Related/EPUB/Content/Images)
  - : Supported image formats, `alt` text and `<figure>` markup, responsive sizing, and cover images.
- [Audio](/en-US/docs/Related/EPUB/Content/Audio)
  - : Embedding audio clips and synchronizing read-aloud narration with SMIL Media Overlays.
- [Video](/en-US/docs/Related/EPUB/Content/Video)
  - : Embedding video clips, exempt video formats, WebVTT captions, and fallbacks for e-ink devices.
- [Scripting](/en-US/docs/Related/EPUB/Content/Scripting)
  - : `<iframe>` vs. spine-level JavaScript, declaring `properties="scripted"`, and keeping content readable when scripts are disabled.
- [`epub:type` attribute](/en-US/docs/Related/EPUB/Content/epub:type)
  - : Adding EPUB structural semantics (such as pop-up footnotes) and pairing them with accessible ARIA `doc-*` roles.
- [Core media types](/en-US/docs/Related/EPUB/Content/Core_media_types)
  - : Complete table of core, foreign, and exempt MIME media types and fallback rules.

## Specifications

- [EPUB 3.3: Publication resources](https://www.w3.org/TR/epub-33/#sec-publication-resources)
- [EPUB 3.3: EPUB content documents](https://www.w3.org/TR/epub-33/#sec-contentdocs)

## See also

- [EPUB content directory](/en-US/docs/Related/EPUB/Structure/Content)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
- [EPUB package spine](/en-US/docs/Related/EPUB/Package/Spine)
