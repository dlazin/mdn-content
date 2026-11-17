---
title: Introduction to EPUB
short-title: Introduction to EPUB
slug: Related/EPUB/Overview
page-type: guide
sidebar: epub
---

**EPUB** (**Electronic Publication**) is the open, vendor-neutral file format for digital books (ebooks) and publications, standardized by the [World Wide Web Consortium (W3C)](https://www.w3.org/TR/epub-33/). An `.epub` file packages [XHTML](/en-US/docs/Glossary/XHTML) text, [CSS](/en-US/docs/Web/CSS) stylesheets, images, fonts, and optional audio or video—along with [XML](/en-US/docs/Web/XML/Guides/XML_introduction) metadata and navigation files—into a single compressed archive that works offline across phones, tablets, computers, and dedicated e-readers.

## Reflowable vs. fixed-layout ebooks

Unlike a PDF, which locks every page to fixed print dimensions, a standard **reflowable EPUB** adapts its layout dynamically to fit the reader's screen and preferences:

- **Adaptive pagination**: Reading systems automatically divide each chapter into screen-sized pages. When a reader rotates a tablet, opens the book on a small phone, or enlarges the font size, line breaks and page counts recalculate on the fly.
- **Reader customization and accessibility**: Readers can change typefaces, line spacing, margins, and color themes (such as dark mode or sepia), or read the book with a screen reader, refreshable braille display, or built-in text-to-speech voice.

EPUB 3 also supports **fixed-layout** publications—where each page has exact pixel dimensions for comics, children's picture books, or heavily illustrated cookbooks—though most trade, academic, and technical ebooks use reflowable layouts so they remain readable and accessible on every device. This documentation focuses on reflowable EPUB 3 publications.

## EPUB 3 and EPUB 2

EPUB was originally developed by the International Digital Publishing Forum (IDPF), which published EPUB 2 in 2007 and EPUB 3 in 2011 before merging into the W3C in 2017. Today, **EPUB 3** (currently at [EPUB 3.3](https://www.w3.org/TR/epub-33/)) is the standard version used across modern publishing workflows and distributors.

Unlike EPUB 2, which relied on XHTML 1.1, a limited subset of CSS 2, and standalone XML navigation formats, EPUB 3:

- Uses modern [HTML](/en-US/docs/Web/HTML) (serialized as [XHTML](/en-US/docs/Glossary/XHTML)), [CSS](/en-US/docs/Web/CSS), [SVG](/en-US/docs/Web/SVG), and [MathML](/en-US/docs/Web/MathML).
- Replaces the legacy XML `.ncx` table of contents and `<guide>` element with an HTML [`<nav>`](/en-US/docs/Related/EPUB/Navigation)-based navigation document (`nav.xhtml`).
- Supports embedded [`<audio>`](/en-US/docs/Related/EPUB/Content/Audio), [`<video>`](/en-US/docs/Related/EPUB/Content/Video), synchronized read-aloud narration (Media Overlays), and optional [JavaScript](/en-US/docs/Related/EPUB/Content/Scripting).
- Adds global language support—including right-to-left page progression, vertical writing modes, and `<ruby>` annotations—alongside rich [accessibility metadata](https://www.w3.org/TR/epub-a11y-11/) and [WAI-ARIA](/en-US/docs/Web/Accessibility/ARIA) roles.

## Reading system and retailer support

EPUB is supported natively across nearly all ebook apps, devices, and lending platforms, including Apple Books, Google Play Books, Rakuten Kobo, Barnes & Noble Nook, PocketBook, OverDrive/Libby, and Readium-based desktop readers such as Thorium Reader.

Amazon Kindle devices and apps do not render `.epub` files directly on the device; instead, Amazon uses EPUB as an input format. Publishers and authors upload `.epub` files to Kindle Direct Publishing (KDP) and Send to Kindle, and Amazon converts those EPUBs into Kindle's proprietary format for delivery to readers. As a result, authoring a valid EPUB 3 file lets you distribute a single master ebook across all major storefronts, libraries, and direct-download channels.

## What is inside an `.epub` file?

Most commercial ebooks are exported from authoring or layout software—such as Adobe InDesign, Pandoc, Pressbooks, Vellum, Sigil, or Calibre—which generates the required container scaffolding automatically. Under the hood, however, every `.epub` file is a [ZIP archive](/en-US/docs/Related/EPUB/Structure/OCF) containing plain text, XML, XHTML, CSS, and media files that you can inspect and edit directly:

1. **[`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype)**: An uncompressed one-line file (`application/epub+zip`) at the very start of the ZIP archive that identifies the file as an EPUB.
2. **[`META-INF/container.xml`](/en-US/docs/Related/EPUB/Structure/META-INF)**: A small XML file that points the reading system to the publication's package file.
3. **[Package file (`.opf`)](/en-US/docs/Related/EPUB/Package)**: An XML file in the [content directory](/en-US/docs/Related/EPUB/Structure/Content) that holds the book's bibliographic [`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata), complete file [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest), and chapter reading-order [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).
4. **[Navigation document (`nav.xhtml`)](/en-US/docs/Related/EPUB/Navigation)**: An XHTML file containing the [table of contents](/en-US/docs/Related/EPUB/Navigation/TOC) and optional [landmarks](/en-US/docs/Related/EPUB/Navigation/Landmarks) and [page lists](/en-US/docs/Related/EPUB/Navigation/Page-list).
5. **[Publication content](/en-US/docs/Related/EPUB/Content)**: The [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) chapter files, [CSS](/en-US/docs/Related/EPUB/Content/CSS) stylesheets, [fonts](/en-US/docs/Related/EPUB/Content/Fonts), [images](/en-US/docs/Related/EPUB/Content/Images), and media assets that readers actually experience.

Because reading systems and retailer ingestion pipelines reject malformed archives or invalid XML, always validate finished `.epub` files with [EPUBCheck](https://www.w3.org/publishing/epubcheck/), the official open-source W3C conformance checker.

## Specifications

- [EPUB 3.3](https://www.w3.org/TR/epub-33/)
- [EPUB Reading Systems 3.3](https://www.w3.org/TR/epub-rs-33/)
- [EPUB Accessibility 1.1](https://www.w3.org/TR/epub-a11y-11/)

## See also

- [EPUB's relationship to the web](/en-US/docs/Related/EPUB/Relationship_to_the_web)
- [EPUB structure](/en-US/docs/Related/EPUB/Structure)
- [EPUB 3 Overview](https://www.w3.org/TR/epub-overview-33/)
