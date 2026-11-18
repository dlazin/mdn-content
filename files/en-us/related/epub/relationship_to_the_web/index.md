---
title: EPUB's relationship to the web
short-title: Relationship to the web
slug: Related/EPUB/Relationship_to_the_web
page-type: guide
sidebar: epub
---

Rather than inventing proprietary formats, [EPUB 3.3](https://www.w3.org/TR/epub-33/#sec-intro-relations) builds directly on Open Web Platform standards, and most EPUB reading systems use browser engines (such as WebKit, Blink, or Gecko) to render books:

- **Markup**: Content and [navigation](/en-US/docs/Related/EPUB/Navigation) use [HTML](/en-US/docs/Web/HTML) serialized as [XHTML](/en-US/docs/Related/EPUB/Content/XHTML), along with [SVG](/en-US/docs/Related/EPUB/Content/SVG) and [MathML](/en-US/docs/Web/MathML). Container and [package](/en-US/docs/Related/EPUB/Package) files use [XML](/en-US/docs/Web/XML/Guides/XML_introduction).
- **Styling**: Layout, typography, [fonts](/en-US/docs/Related/EPUB/Content/Fonts), and media queries use [CSS](/en-US/docs/Related/EPUB/Content/CSS).
- **Media and interactivity**: Images, fonts, and audio use standard web [core media types](/en-US/docs/Related/EPUB/Content/Core_media_types), alongside HTML [`<audio>`](/en-US/docs/Related/EPUB/Content/Audio), [`<video>`](/en-US/docs/Related/EPUB/Content/Video), and optional [JavaScript](/en-US/docs/Related/EPUB/Content/Scripting).
- **Accessibility**: Accessible ebooks rely on semantic HTML, `alt` text, [WCAG](https://www.w3.org/WAI/standards-guidelines/wcag/), and [ARIA](/en-US/docs/Web/Accessibility/ARIA) roles from the [Digital Publishing WAI-ARIA Module](https://www.w3.org/TR/dpub-aria-1.1/).

Although EPUB points to living web standards, EPUB itself is a versioned specification, and most web browsers do not open `.epub` files natively without an extension. Newly standardized HTML and CSS features may be syntactically valid in EPUB while still unsupported by older reading systems.

If you already know HTML and CSS, keep these key differences in mind when creating an EPUB:

## Strict XHTML syntax

Web browsers parse HTML leniently and recover silently from missing closing tags or unescaped ampersands. By contrast, EPUBs are governed by a strict XML parser. For example:

- Every element must be properly closed (`<img src="..." alt="..." />`, `<br />`, `<meta charset="utf-8" />`).
- Boolean attributes must have explicit values (`hidden=""` or `controls="controls"`, never bare `hidden`).
- Only the five predefined XML character entities (`&amp;`, `&lt;`, `&gt;`, `&quot;`, and `&apos;`) are recognized; other named HTML entities like `&mdash;` are not permitted. Instead, special characters must be represented as numeric entities (like `&#160;`) or directly as UTF-8 (like `—`).

A single XML syntax error causes reading systems to halt and display an error message instead of rendering the chapter. See [EPUB XHTML](/en-US/docs/Related/EPUB/Content/XHTML) for details.

## Self-contained offline packaging

Websites fetch pages, stylesheets, images, and APIs on demand from remote servers. An EPUB is designed to be downloaded once and read offline anywhere—including on airplane-mode tablets and offline e-readers—for years to come. Consequently:

- All files are bundled inside a single [OCF ZIP archive](/en-US/docs/Related/EPUB/Structure/OCF).
- Every resource must be listed in the package [manifest](/en-US/docs/Related/EPUB/Package/Manifest).
- Documents, stylesheets, and images must be stored inside the container. Only audio, video, fonts, and scripted data can be fetched from remote URLs, and only when flagged in the manifest with `properties="remote-resources"`.

## Book metadata

Book-level bibliographic data (title, author, ISBN, language, publication date) lives in the package [metadata](/en-US/docs/Related/EPUB/Package/Metadata) rather than in HTML `<meta>` tags.

## Reading order

A website connects pages through links and URL routing with no specific sequence, whereas a book has a defined start-to-finish reading order. Instead of putting an entire book into one large XHTML file (which might behave slowly on low-memory e-readers), an EPUB typically splits each chapter into its own file, then sequences them in the package [spine](/en-US/docs/Related/EPUB/Package/Spine) (which controls document order) and [navigation](/en-US/docs/Related/EPUB/Navigation) (used to select chapters in the reading system's UI).

## Pagination and user restyling

Web browsers give designers full control over layout. EPUB reading systems paginate reflowable chapters into screen-sized columns and prioritize reader control, typically letting users change typography, margins, and themes (light, dark, sepia). See [EPUB CSS](/en-US/docs/Related/EPUB/Content/CSS) to learn about safe styling patterns.

## Core media types

While web browsers negotiate formats dynamically and update continuously, EPUB guarantees support only for a defined set of [core media types](/en-US/docs/Related/EPUB/Content/Core_media_types). Newer or specialized formats (such as AVIF images) require an explicit fallback to a core format.

## Optional and sandboxed JavaScript

JavaScript is foundational on modern websites, whereas ebooks rarely contain [scripting](/en-US/docs/Related/EPUB/Content/Scripting). Many EPUB reading systems—especially e-ink devices and conversion pipelines—disable or heavily restrict scripting. Any scripted interactivity in an EPUB should use progressive enhancement so the book remains readable when scripts are blocked.

## Specifications

- [EPUB 3.3: Relationship to other specifications](https://www.w3.org/TR/epub-33/#sec-intro-relations)
- [EPUB Reading Systems 3.3](https://www.w3.org/TR/epub-rs-33/)

## See also

- [Introduction to EPUB](/en-US/docs/Related/EPUB/Overview)
- [EPUB publication content](/en-US/docs/Related/EPUB/Content)
- [EPUB XHTML](/en-US/docs/Related/EPUB/Content/XHTML)
- [EPUB CSS](/en-US/docs/Related/EPUB/Content/CSS)
