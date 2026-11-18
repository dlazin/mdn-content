---
title: Introduction to EPUB
slug: Related/EPUB/Overview
page-type: guide
sidebar: epub
---

EPUB (Electronic Publication) is the open, vendor-neutral file format for digital books (ebooks) and publications, standardized by the [World Wide Web Consortium (W3C)](https://www.w3.org/TR/epub-33/). An EPUB packages [XHTML](/en-US/docs/Glossary/XHTML) text and optional [CSS](/en-US/docs/Web/CSS) stylesheets, images, fonts, audio, or video—along with [XML](/en-US/docs/Web/XML/Guides/XML_introduction) metadata and navigation files—into a single compressed archive that works offline across phones, tablets, computers, and dedicated e-readers.

## Reflow vs. fixed layout

Unlike a PDF, which locks every page to fixed dimensions, a reflowable EPUB adapts its layout dynamically to fit the reader's screen and preferences. Reading systems automatically divide each chapter into screen-sized pages. When a reader rotates a tablet, opens the book on a small phone, or enlarges the font size, line breaks and page counts recalculate on the fly.

For reflowable books, reading systems generally let users change typefaces, line spacing, margins, and color themes (such as dark mode or sepia), or read the book with a screen reader, refreshable braille display, or text-to-speech voice.

EPUB 3 also supports fixed-layout publications, where each page is predesigned to a fixed size. Fixed layout is most frequently used for children's picture books or heavily illustrated cookbooks.

Most ebooks use reflowable layouts so they remain easy to read on any device. This documentation focuses on reflowable publications.

## EPUB 3 and EPUB 2

EPUB was first standardized by the [International Digital Publishing Forum (IDPF)](https://idpf.org/), which published the specification for the Open eBook Publication Structure in 1999, followed by EPUB 2 in 2007 and EPUB 3 in 2011. The IDPF merged into the W3C in 2017. The current [EPUB 3.3](https://www.w3.org/TR/epub-33/) recommendation, released in 2023, is backward-compatible with previous versions of EPUB 3 and thus supported by most publishing workflows and distributors.

Where EPUB 2 relied on XHTML 1.1, a limited subset of CSS 2, and standalone XML NCX navigation files, EPUB 3 adopts modern [web standards](/en-US/docs/Related/EPUB/Relationship_to_the_web), an HTML-based [navigation document](/en-US/docs/Related/EPUB/Reference/Navigation), embedded audio and video (including synchronized Media Overlays), global typography such as right-to-left and vertical writing modes, and rich [accessibility metadata](https://www.w3.org/TR/epub-a11y-11/).

## App and retailer support

EPUB is supported natively across nearly all ebook apps, devices, retailers, and lending platforms, including Apple Books, Google Play Books, Kobo, Libby, Nook, PocketBook, and Readium-based desktop readers such as Thorium Reader.

Amazon Kindle devices and apps do not render EPUB directly on the device; instead, Amazon uses EPUB as an input format. Publishers and authors upload EPUB files to Kindle Direct Publishing (KDP) or the Send to Kindle service, and Amazon converts those EPUBs into Kindle's proprietary format.

## Creating an EPUB

Most commercial ebooks are exported from Adobe InDesign after the print book has been laid out. Many authoring programs can also generate EPUBs, including Google Docs, Apple Pages, and Scrivener.

When software exports an EPUB, it generates the required [container structure](/en-US/docs/Related/EPUB/Reference/Structure), [package file](/en-US/docs/Related/EPUB/Reference/Package), and [navigation document](/en-US/docs/Related/EPUB/Reference/Navigation) automatically. Under the hood, every EPUB is a specialized [ZIP archive](/en-US/docs/Related/EPUB/Reference/Structure/OCF) whose XML, XHTML, CSS, and media files you can unzip, inspect, and edit.

Because reading systems and retailer pipelines reject malformed EPUBs, completed EPUBs should always be validated with [EPUBCheck](https://www.w3.org/publishing/epubcheck/), the W3C's conformance checker.

## Specifications

- [EPUB 3.3](https://www.w3.org/TR/epub-33/)
- [EPUB 3.3 Overview](https://www.w3.org/TR/epub-overview-33/)
- [EPUB Reading Systems 3.3](https://www.w3.org/TR/epub-rs-33/)
- [EPUB Accessibility 1.1](https://www.w3.org/TR/epub-a11y-11/)

## See also

- [How EPUB differs from the web](/en-US/docs/Related/EPUB/Differences_from_the_web)
