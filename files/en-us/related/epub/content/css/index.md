---
title: EPUB CSS stylesheets
short-title: CSS
slug: Related/EPUB/Content/CSS
page-type: guide
sidebar: epub
---

EPUB 3 uses standard [Cascading Style Sheets (CSS)](/en-US/docs/Web/CSS) to style [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) and [SVG](/en-US/docs/Related/EPUB/Content/SVG) content documents. Every `.css` file must be encoded in UTF-8 (or UTF-16, with UTF-8 recommended), stored inside the EPUB container, declared in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) with `media-type="text/css"`, and linked from each content document that uses it.

## Designing CSS for reflowable ebooks

Although modern reading systems parse CSS with browser engines, styling a reflowable ebook differs from styling a website because readers customize the display and reading apps paginate chapters dynamically.

### Dark mode and reader color themes

Most reading apps provide white, sepia, gray, and dark ("night") themes. Do not set `color: #000000` (or `color: black`) on body text: in dark mode, some reading systems darken the page background without overriding author text colors, leaving black text unreadable on a dark background.

Leave body `color` and `background-color` unset so the reader's chosen theme applies cleanly. Whenever you set a `background-color` on a callout box, set a contrasting `color` on that same element, or style borders with `currentColor` instead of solid fills.

### Relative units and user overrides

Size headings, paragraphs, indents, and spacing with relative units (`rem`, `em`, `%`) rather than fixed `px` or `pt` values so text scales proportionally when the reader changes font size. Avoid `!important`, which can block font, line-height, and spacing overrides used by readers with low vision or dyslexia.

### Dynamic pagination

Reflowable reading systems usually paginate chapters by laying out content inside CSS multi-column containers. Avoid `position: fixed`, `position: absolute`, and rigid viewport heights (`height: 100vh`), which interact poorly with column pagination and can clip or hide overflowing text. Use `break-inside: avoid` on `<figure>`, `<blockquote>`, and table elements where supported to reduce mid-element page splits.

## Prohibited CSS properties

EPUB 3 allows any standard CSS property except two directional properties, which must not appear in EPUB stylesheets:

- `direction`
- `unicode-bidi`

Because a reading system may disable CSS or render text non-visually (such as text-to-speech or refreshable braille), setting right-to-left or bidirectional text order only in CSS would scramble text when stylesheets are not applied. Declare text direction in markup instead using the HTML [`dir`](/en-US/docs/Web/HTML/Reference/Global_attributes/dir) attribute (`dir="rtl"`, `dir="ltr"`, or `dir="auto"`) and [`<bdo>`](/en-US/docs/Web/HTML/Reference/Elements/bdo) (or SVG's `direction` attribute).

> [!NOTE]
> Vertical text layout via CSS [`writing-mode`](/en-US/docs/Web/CSS/Reference/Properties/writing-mode) (such as `writing-mode: vertical-rl` for vertically typeset Japanese or Chinese ebooks) is allowed in EPUB CSS and is usually paired with `page-progression-direction="rtl"` on [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).

## Legacy `-epub-` prefixed properties

Before CSS Writing Modes, CSS Text Level 3, and CSS Text Decoration Level 3 matured on the web, earlier versions of EPUB 3 defined `-epub-` prefixed equivalents for international typography. EPUB 3.3 keeps several of these for backward compatibility, including `-epub-writing-mode`, `-epub-text-combine-horizontal`, `-epub-hyphens`, `-epub-line-break`, `-epub-word-break`, `-epub-text-align-last`, `-epub-text-emphasis-style`, `-epub-text-emphasis-color`, `-epub-text-emphasis-position`, and `-epub-text-underline-position`.

In new EPUBs, use standard unprefixed CSS properties (`writing-mode`, `text-combine-upright`, `hyphens`, `line-break`, `word-break`, `text-align-last`, `text-emphasis`, and `text-underline-position`). Future major versions of EPUB plan to drop `-epub-` prefixes.

## Examples

### Reflowable chapter stylesheet

This `styles/main.css` stylesheet uses relative units, book-style first-line paragraph indents, theme-safe `currentColor` borders, and responsive figure sizing:

```css
body {
  line-height: 1.5;
  margin: 0;
  padding: 0;
}

h1,
h2,
h3 {
  line-height: 1.25;
  margin-block: 1.5em 0.75em;
  break-after: avoid;
}

/* Indent consecutive paragraphs rather than adding vertical gaps */
p {
  margin: 0;
  text-indent: 1.5em;
}

h1 + p,
h2 + p,
h3 + p,
blockquote p,
figure + p {
  text-indent: 0;
}

/* Use currentColor so borders adapt to sepia and dark modes */
blockquote {
  margin: 1em 1.5em;
  padding-inline-start: 1em;
  border-inline-start: 2px solid currentColor;
}

figure {
  margin: 1.5em 0;
  text-align: center;
  break-inside: avoid;
}

figure img {
  max-width: 100%;
  height: auto;
}

figcaption {
  font-size: 0.9em;
  margin-top: 0.5em;
}
```

Declare the stylesheet in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) and link it from `<head>` in your XHTML files:

```xml
<!-- In package.opf -->
<item id="main-css" href="styles/main.css" media-type="text/css" />

<!-- In text/chapter-1.xhtml -->
<link rel="stylesheet" type="text/css" href="../styles/main.css" />
```

## Specifications

- [EPUB 3.3: Cascading Style Sheets (CSS)](https://www.w3.org/TR/epub-33/#sec-css)
- [EPUB 3.3: CSS requirements](https://www.w3.org/TR/epub-33/#sec-css-req)
- [EPUB 3.3: Prefixed CSS properties](https://www.w3.org/TR/epub-33/#css-prefixes)

## See also

- [CSS: Cascading Style Sheets](/en-US/docs/Web/CSS)
- [EPUB fonts](/en-US/docs/Related/EPUB/Content/Fonts)
- [EPUB XHTML content documents](/en-US/docs/Related/EPUB/Content/XHTML)
- [`dir` HTML global attribute](/en-US/docs/Web/HTML/Reference/Global_attributes/dir)
