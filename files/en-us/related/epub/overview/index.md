---
title: Introduction to EPUB
short-title: Introduction to EPUB
slug: Related/EPUB/Overview
page-type: guide
sidebar: epub
---

**EPUB** (**Electronic Publication**) is the open, vendor-neutral file format for digital books (ebooks) and publications, standardized by the [World Wide Web Consortium (W3C)](https://www.w3.org/TR/epub-33/). An `.epub` file packages [XHTML](/en-US/docs/Glossary/XHTML) text and optional [CSS](/en-US/docs/Web/CSS) stylesheets, images, fonts, audio or video—along with [XML](/en-US/docs/Web/XML/Guides/XML_introduction) metadata and navigation files—into a single compressed archive that works offline across phones, tablets, computers, and dedicated e-readers.

## Reflow vs. fixed layout

Unlike a PDF, which locks every page to fixed dimensions, a reflowable EPUB adapts its layout dynamically to fit the reader's screen and preferences. Reading systems automatically divide each chapter into screen-sized pages. When a reader rotates a tablet, opens the book on a small phone, or enlarges the font size, line breaks and page counts recalculate on the fly.

For reflowable books, reading systems generally let users change typefaces, line spacing, margins, and color themes (such as dark mode or sepia), or read the book with a screen reader, refreshable braille display, or text-to-speech voice.

EPUB 3 also supports fixed-layout publications, where each page is predesigned to a fixed size. Fixed layout is most frequently used for children's picture books or heavily illustrated cookbooks.

Most ebooks use reflowable layouts so they remain easy to read on any device. This documentation focuses on reflowable publications.

## EPUB 3 and EPUB 2

EPUB was first standardized by the [International Digital Publishing Forum (IDPF)](https://idpf.org/), which published the specification for the Open eBook Publication Structure in 1999, followed by EPUB 2 in 2007 and EPUB 3 in 2011. The IDPF merged into the W3C in 2017. The current [EPUB 3.3](https://www.w3.org/TR/epub-33/) recommendation, released in 2024, is backward-compatible with previous versions of EPUB 3 and thus supported by most publishing workflows and distributors.

Unlike EPUB 2, which relied on XHTML 1.1, a limited subset of CSS 2, and standalone XML navigation formats, EPUB 3:

- Uses semi-modern [HTML](/en-US/docs/Web/HTML) (serialized as [XHTML](/en-US/docs/Glossary/XHTML)), [CSS](/en-US/docs/Web/CSS), [SVG](/en-US/docs/Web/SVG), and [MathML](/en-US/docs/Web/MathML).
- Replaces the legacy XML `.ncx` table of contents with an HTML-based [navigation document](/en-US/docs/Related/EPUB/Navigation).
- Supports embedded [`<audio>`](/en-US/docs/Related/EPUB/Content/Audio), [`<video>`](/en-US/docs/Related/EPUB/Content/Video), synchronized read-aloud narration (Media Overlays), and [JavaScript](/en-US/docs/Related/EPUB/Content/Scripting).
- Adds global language support—including right-to-left text and page progression and vertical writing modes—alongside rich [accessibility metadata](https://www.w3.org/TR/epub-a11y-11/) and [ARIA](/en-US/docs/Web/Accessibility/ARIA) roles.

## App and retailer support

EPUB is supported natively across nearly all ebook apps, devices, retailers, and lending platforms, including Apple Books, Google Play Books, Kobo, Libby, Nook, PocketBook, and Readium-based desktop readers such as Thorium Reader.

Amazon Kindle devices and apps do not render EPUB directly on the device; instead, Amazon uses EPUB as an input format. Publishers and authors upload `.epub` files to Kindle Direct Publishing (KDP) or the Send to Kindle service, and Amazon converts those EPUBs into Kindle's proprietary format.

## Creating an EPUB

Most commercial ebooks are exported from authoring or layout software—most commonly Adobe InDesign, after the print book has been laid out. The export operation generates the required container scaffolding automatically. Under the hood, however, every `.epub` file is a specialized [ZIP archive](/en-US/docs/Related/EPUB/Structure/OCF) containing XML, XHTML, CSS, and media files that you can inspect and edit directly:

- [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype): An uncompressed one-line file at the very start of the ZIP archive that identifies the file as an EPUB.
- [`META-INF/container.xml`](/en-US/docs/Related/EPUB/Structure/META-INF): A small XML file that points the reading system to the publication's package file.
- [Package file (`.opf`)](/en-US/docs/Related/EPUB/Package): An XML file in the [content directory](/en-US/docs/Related/EPUB/Structure/Content) that holds the [bibliographic metadata](/en-US/docs/Related/EPUB/Package/Metadata), [file manifest](/en-US/docs/Related/EPUB/Package/Manifest), and [reading order](/en-US/docs/Related/EPUB/Package/Spine).
- [Navigation document](/en-US/docs/Related/EPUB/Navigation): An XHTML file containing the [table of contents](/en-US/docs/Related/EPUB/Navigation/TOC) and optional [landmarks](/en-US/docs/Related/EPUB/Navigation/Landmarks) and [page lists](/en-US/docs/Related/EPUB/Navigation/Page-list).
- [Publication content](/en-US/docs/Related/EPUB/Content): The [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) chapter files, [CSS](/en-US/docs/Related/EPUB/Content/CSS) stylesheets, [fonts](/en-US/docs/Related/EPUB/Content/Fonts), [images](/en-US/docs/Related/EPUB/Content/Images), and other media assets that readers actually experience.

Because reading systems and retailer pipelines reject malformed EPUBs, completed `.epub` files should always be validated with [EPUBCheck](https://www.w3.org/publishing/epubcheck/), the W3C's conformance checker.

## Specifications

- [EPUB 3.3](https://www.w3.org/TR/epub-33/)
- [EPUB Reading Systems 3.3](https://www.w3.org/TR/epub-rs-33/)
- [EPUB Accessibility 1.1](https://www.w3.org/TR/epub-a11y-11/)

## See also

- [EPUB's relationship to the web](/en-US/docs/Related/EPUB/Relationship_to_the_web)
- [EPUB structure](/en-US/docs/Related/EPUB/Structure)
- [EPUB 3 Overview](https://www.w3.org/TR/epub-overview-33/)
