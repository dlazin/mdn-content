---
title: "EPUB: Open ebooks"
short-title: EPUB
slug: Related/EPUB
page-type: landing-page
sidebar: epub
---

**EPUB** (**Electronic Publication**) is the W3C open standard file format for digital books and publications. An `.epub` file is a specialized ZIP archive that packages web content—such as [XHTML](/en-US/docs/Glossary/XHTML), [CSS](/en-US/docs/Web/CSS), [SVG](/en-US/docs/Web/SVG), [MathML](/en-US/docs/Web/MathML), images, fonts, and audio—together with [XML](/en-US/docs/Web/XML/Guides/XML_introduction) metadata and navigation files so reading systems can present a complete book offline across e-readers, phones, tablets, and computers.

This documentation covers **EPUB 3**, focusing on reflowable ebooks whose text and layout adapt dynamically to different screen sizes, font settings, and assistive technologies.

## Guides and concepts

- [Introduction to EPUB](/en-US/docs/Related/EPUB/Overview)
  - : An overview of what EPUB is, where it is supported across reading systems and storefronts, and how an ebook's files fit together.
- [EPUB's relationship to the web](/en-US/docs/Related/EPUB/Relationship_to_the_web)
  - : How EPUB builds on HTML, CSS, SVG, and other Open Web Platform standards—and where ebook packaging, pagination, and offline rules differ from web browsers.

## Reference

- [EPUB structure](/en-US/docs/Related/EPUB/Structure)
  - : The container layout of an `.epub` archive, including the [Open Container Format (OCF)](/en-US/docs/Related/EPUB/Structure/OCF), the [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype) file, the [`META-INF`](/en-US/docs/Related/EPUB/Structure/META-INF) directory (`container.xml`), and the [content directory](/en-US/docs/Related/EPUB/Structure/Content).
- [EPUB package file](/en-US/docs/Related/EPUB/Package)
  - : The `.opf` XML file that acts as the central index of the publication, declaring its bibliographic [`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata), resource [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest), and default reading-order [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).
- [EPUB navigation document](/en-US/docs/Related/EPUB/Navigation)
  - : The required XHTML navigation file (`nav.xhtml`) that provides the [table of contents (`toc`)](/en-US/docs/Related/EPUB/Navigation/TOC), structural [landmarks](/en-US/docs/Related/EPUB/Navigation/Landmarks), and static [page lists](/en-US/docs/Related/EPUB/Navigation/Page-list).
- [EPUB publication content](/en-US/docs/Related/EPUB/Content)
  - : Authoring rules and format support for [XHTML](/en-US/docs/Related/EPUB/Content/XHTML), [SVG](/en-US/docs/Related/EPUB/Content/SVG), [CSS](/en-US/docs/Related/EPUB/Content/CSS), [fonts](/en-US/docs/Related/EPUB/Content/Fonts), [images](/en-US/docs/Related/EPUB/Content/Images), [audio and Media Overlays](/en-US/docs/Related/EPUB/Content/Audio), [video](/en-US/docs/Related/EPUB/Content/Video), [scripting](/en-US/docs/Related/EPUB/Content/Scripting), [`epub:type`](/en-US/docs/Related/EPUB/Content/epub:type), and [core media types](/en-US/docs/Related/EPUB/Content/Core_media_types).

## Specifications

- [EPUB 3.3](https://www.w3.org/TR/epub-33/)
- [EPUB Reading Systems 3.3](https://www.w3.org/TR/epub-rs-33/)
- [EPUB Accessibility 1.1](https://www.w3.org/TR/epub-a11y-11/)

## See also

- [EPUB 3 Overview (W3C Working Group Note)](https://www.w3.org/TR/epub-overview-33/)
- [DAISY Accessible Publishing Knowledge Base](https://kb.daisy.org/publishing/docs/)
- [EPUBCheck](https://www.w3.org/publishing/epubcheck/) — the official open-source conformance checker for EPUB publications
