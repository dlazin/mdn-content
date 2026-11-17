---
title: EPUB's relationship to the web
short-title: Relationship to the web
slug: Related/EPUB/Relationship_to_the_web
page-type: guide
sidebar: epub
---

At its core, an EPUB 3 ebook is built with the same Open Web Platform technologies used to build websites: [HTML](/en-US/docs/Web/HTML) (written in its XML syntax, [XHTML](/en-US/docs/Glossary/XHTML)), [CSS](/en-US/docs/Web/CSS), [SVG](/en-US/docs/Web/SVG), [MathML](/en-US/docs/Web/MathML), [WAI-ARIA](/en-US/docs/Web/Accessibility/ARIA), standard web font and media formats, and optional [JavaScript](/en-US/docs/Web/JavaScript). Modern EPUB reading systems render books using browser engines (such as WebKit, Blink, or Gecko), which means web development skills transfer directly to ebook production.

At the same time, web browsers do not open `.epub` files natively without an extension or web app, and reading an offline, paginated book across e-ink devices, phones, and screen readers imposes rules that do not exist on ordinary websites.

## Shared web standards

Rather than inventing proprietary text or styling formats, [EPUB 3.3](https://www.w3.org/TR/epub-33/#sec-intro-relations) references living and evergreen web standards directly:

- **Document markup**: Chapters and the [navigation document](/en-US/docs/Related/EPUB/Navigation) use elements and attributes from the [HTML](/en-US/docs/Web/HTML) standard, serialized as XML ([XHTML](/en-US/docs/Related/EPUB/Content/XHTML)), along with embedded [SVG](/en-US/docs/Related/EPUB/Content/SVG) and Presentation [MathML](/en-US/docs/Web/MathML).
- **Styling and typography**: Layout, typography, custom [`@font-face`](/en-US/docs/Related/EPUB/Content/Fonts) fonts, and media queries come from [CSS](/en-US/docs/Related/EPUB/Content/CSS) as defined by the W3C CSS Snapshot.
- **Media**: Illustrations and audio clips use standard web [core media types](/en-US/docs/Related/EPUB/Content/Core_media_types)—PNG, JPEG, WebP, GIF, SVG, WOFF2, WOFF, OpenType, TrueType, MP3, AAC MP4, and Ogg Opus—alongside HTML [`<audio>`](/en-US/docs/Related/EPUB/Content/Audio) and [`<video>`](/en-US/docs/Related/EPUB/Content/Video) elements.
- **Accessibility**: Accessible ebooks rely on semantic HTML, `alt` text, [WCAG](https://www.w3.org/WAI/standards-guidelines/wcag/), and publishing roles from the [Digital Publishing WAI-ARIA Module](https://www.w3.org/TR/dpub-aria-1.1/).

Because EPUB points to living web specifications, newly standardized HTML and CSS features automatically become valid in EPUB—though ebook authors still need to test whether older e-reader firmware supports newer CSS properties before relying on them.

## How EPUB differs from the open web

If you already know HTML and CSS, keep six key differences in mind when creating an EPUB:

### 1. Strict XML syntax (XHTML)

On the web, browsers parse `text/html` leniently and recover silently from missing closing tags or unescaped ampersands. In EPUB, content documents have the MIME type `application/xhtml+xml` and are parsed by a strict XML parser:

- Every element must be properly closed (`<img src="..." alt="..." />`, `<br />`, `<meta charset="utf-8" />`).
- Boolean attributes must have explicit values (`hidden=""` or `controls="controls"`, never bare `hidden`).
- Only the five predefined XML character entities (`&amp;`, `&lt;`, `&gt;`, `&quot;`, and `&apos;`) are recognized; write characters such as `—` or non-breaking spaces directly as UTF-8 (or use numeric references like `&#160;`) instead of HTML named entities like `&mdash;` or `&nbsp;`.

A single XML syntax error causes reading systems to halt and display an error message instead of rendering the chapter. See [EPUB XHTML](/en-US/docs/Related/EPUB/Content/XHTML) for details.

### 2. Self-contained offline packaging

Websites fetch pages, stylesheets, images, and APIs on demand from remote servers. An `.epub` file is designed to be downloaded once and read offline anywhere—including on airplane-mode tablets and offline e-readers—for years to come:

- All files are bundled inside a single [OCF ZIP archive](/en-US/docs/Related/EPUB/Structure/OCF).
- Every publication resource must be listed in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest); undeclared files in the archive fail validation.
- XHTML documents, CSS stylesheets, and images **must** be stored inside the container. Only audio, video, fonts, and script-fetched data are allowed to live at remote `https:` URLs, and only when flagged with `properties="remote-resources"` in `<manifest>`.

### 3. Multi-document reading order and book metadata

A website connects pages through navigation bars, links, and URL routing, with no single global sequence of pages. A book has a defined start-to-finish reading order and a catalog record:

- Instead of putting an entire book into one huge HTML file (which would load slowly on low-memory e-readers), authors typically split each chapter into its own `.xhtml` file and sequence them in the package [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).
- A dedicated [navigation document](/en-US/docs/Related/EPUB/Navigation) (`nav.xhtml`) exposes a machine-readable [table of contents](/en-US/docs/Related/EPUB/Navigation/TOC), [landmarks](/en-US/docs/Related/EPUB/Navigation/Landmarks), and [page list](/en-US/docs/Related/EPUB/Navigation/Page-list) to the reading system's UI.
- Book-level bibliographic data (title, creators, ISBN/UUID, language, modification date) lives in the package [`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata) rather than in HTML `<meta>` tags.

### 4. Dynamic pagination and user style overrides

Web browsers scroll vertically by default and give the page author full control over colors, fonts, and viewport layout. Reflowable EPUB reading systems paginate chapters into screen-sized columns and prioritize **reader control**:

- Readers routinely change font families, font sizes, line heights, margins, and background/foreground themes (light, sepia, dark).
- Hardcoding body font sizes in `px`, setting `color: #000000` on paragraphs, or forcing fixed heights on text containers breaks dark mode and causes text to clip across page breaks. See [EPUB CSS](/en-US/docs/Related/EPUB/Content/CSS) for pagination-safe styling patterns.

### 5. Core media types and fallbacks

While web browsers negotiate formats dynamically and update continuously, ebooks are opened across a wide span of dedicated e-ink devices, mobile apps, and assistive tools. EPUB guarantees support only for a defined set of [core media types](/en-US/docs/Related/EPUB/Content/Core_media_types); newer or specialized formats (such as AVIF images) require an explicit fallback to a core format.

### 6. Optional and sandboxed JavaScript

JavaScript is foundational on modern websites, whereas most ebooks contain no scripts at all. Many EPUB reading systems—especially e-ink devices and conversion pipelines—disable [scripting](/en-US/docs/Related/EPUB/Content/Scripting) or restrict scripts from modifying page dimensions, accessing network APIs, or persisting storage across chapters. Any scripted interactivity in a reflowable EPUB should use progressive enhancement so the text remains readable when scripts do not run.

## Specifications

- [EPUB 3.3: Relationship to other specifications](https://www.w3.org/TR/epub-33/#sec-intro-relations)
- [EPUB Reading Systems 3.3](https://www.w3.org/TR/epub-rs-33/)

## See also

- [Introduction to EPUB](/en-US/docs/Related/EPUB/Overview)
- [EPUB publication content](/en-US/docs/Related/EPUB/Content)
- [EPUB XHTML](/en-US/docs/Related/EPUB/Content/XHTML)
- [EPUB CSS](/en-US/docs/Related/EPUB/Content/CSS)
