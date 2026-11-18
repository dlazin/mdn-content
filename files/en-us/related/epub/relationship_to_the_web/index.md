---
title: EPUB's relationship to the web
short-title: Relationship to the web
slug: Related/EPUB/Relationship_to_the_web
page-type: guide
sidebar: epub
---

An EPUB 3 ebook is built with the same Open Web Platform technologies used to build websites: [HTML](/en-US/docs/Web/HTML) (written in its XML syntax, [XHTML](/en-US/docs/Glossary/XHTML)), [CSS](/en-US/docs/Web/CSS), [SVG](/en-US/docs/Web/SVG), [MathML](/en-US/docs/Web/MathML), [XML](/en-US/docs/Web/XML/Guides/XML_introduction), [ARIA](/en-US/docs/Web/Accessibility/ARIA), standard web font and media formats, and [JavaScript](/en-US/docs/Web/JavaScript). This means that web development skills transfer directly to ebook production. Most EPUB reading systems use browser engines (such as WebKit, Blink, or Gecko) to render books.

At the same time, working on an EPUB is [not quite the same as a website](/en-US/docs/Related/EPUB/Differences_from_the_web).

## Shared web standards

Rather than inventing proprietary formats, the EPUB 3.3 spec [references existing web standards](https://www.w3.org/TR/epub-33/#sec-intro-relations):

- [Content](/en-US/docs/Related/EPUB/Content) and [navigation](/en-US/docs/Related/EPUB/Navigation) use [HTML](/en-US/docs/Web/HTML) serialized as [XHTML](/en-US/docs/Related/EPUB/Content/XHTML), along with [SVG](/en-US/docs/Related/EPUB/Content/SVG) and [MathML](/en-US/docs/Web/MathML). Container and [package](/en-US/docs/Related/EPUB/Package) files use [XML](/en-US/docs/Web/XML/Guides/XML_introduction).
- Layout, typography, [fonts](/en-US/docs/Related/EPUB/Content/Fonts), and media queries use [CSS](/en-US/docs/Related/EPUB/Content/CSS).
- Images, fonts, and audio use [core media types](/en-US/docs/Related/EPUB/Content/Core_media_types) drawn from the web, alongside HTML [audio](/en-US/docs/Related/EPUB/Content/Audio), [video](/en-US/docs/Related/EPUB/Content/Video), and optional [JavaScript](/en-US/docs/Related/EPUB/Content/Scripting).
- Accessible ebooks rely on semantic HTML, `alt` text, [WCAG](https://www.w3.org/WAI/standards-guidelines/wcag/), and [ARIA](/en-US/docs/Web/Accessibility/ARIA) roles from the [Digital Publishing WAI-ARIA Module](https://www.w3.org/TR/dpub-aria-1.1/).

Although EPUB points to living web standards, EPUB itself remains a versioned specification. Newly standardized HTML and CSS features may be syntactically valid in EPUB while still unsupported by older reading systems.

## Specifications

- [EPUB 3.3: Relationship to other specifications](https://www.w3.org/TR/epub-33/#sec-intro-relations)
- [EPUB Reading Systems 3.3: Relationship to other specifications](https://www.w3.org/TR/epub-rs-33/#sec-intro-relations)
- [EPUB Accessibility 1.1: Relationship to WCAG](https://www.w3.org/TR/epub-a11y-11/#sec-rel-wcag)

## See also

- [Introduction to EPUB](/en-US/docs/Related/EPUB/Overview)
- [How EPUB differs from the web](/en-US/docs/Related/EPUB/Differences_from_the_web)
