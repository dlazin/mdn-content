---
title: EPUB CSS stylesheets
short-title: CSS
slug: Related/EPUB/Content/CSS
page-type: guide
sidebar: epub
---

EPUB 3 uses standard [Cascading Style Sheets (CSS)](/en-US/docs/Web/CSS) to control typography, spacing, borders, and layout across [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) and [SVG](/en-US/docs/Related/EPUB/Content/SVG) content documents. Every `.css` file must be encoded in UTF-8 (or UTF-16, with UTF-8 strongly recommended), stored inside the EPUB container, declared in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) with `media-type="text/css"`, and linked from each content document that uses it.

## Designing CSS for reflowable ebooks

While EPUB reading systems use modern browser engines to parse CSS, styling a reflowable ebook requires different habits than styling a fixed-brand website because readers control the reading environment and reading systems dynamically paginate the text:

- **Respect dark mode and reader color themes**: Most reading apps offer white, sepia, gray, and black ("night") reading modes. Never set `color: #000000` (or `color: black`) on body prose—in night mode, some reading systems darken the page background without overriding hardcoded inline or element colors, rendering black text unreadable against a dark background. Leave body text `color` and `background-color` unset so the reading system's theme applies cleanly, and whenever you set a custom `background-color` on a callout box, explicitly set a contrasting `color` on that same box (or style borders with `currentColor` instead of solid fills).
- **Use relative units for typography**: Size headings, paragraphs, indents, and vertical spacing with relative units (`rem`, `em`, `%`) rather than fixed `px` or `pt` values so text scales proportionally when the user increases or decreases font size.
- **Account for dynamic column pagination**: Reflowable reading systems typically paginate chapters by laying out the document inside CSS multi-column containers behind the scenes. Avoid `position: fixed` and `position: absolute`, which interact unpredictably with column pagination and can push content off-screen. Use `break-inside: avoid` on `<figure>`, `<blockquote>`, and table elements where supported to reduce awkward mid-element page splits, and avoid rigid heights (`height: 100vh`) that can clip text when font sizes increase.
- **Avoid `!important`**: Users with low vision or dyslexia frequently override fonts, line heights, and paragraph spacing through reading system settings; `!important` declarations can block those accessibility adjustments.

## Prohibited CSS properties

EPUB 3 allows any standard CSS property **except** two directional properties, which **must not** appear in EPUB stylesheets:

- `direction` (prohibited)
- `unicode-bidi` (prohibited)

Because a reading system may disable CSS or lack visual CSS rendering (for example, a text-to-speech or refreshable Braille system), expressing right-to-left or bidirectional text order only in CSS would scramble the text when stylesheets are turned off. Always declare text directionality in markup instead, using the HTML [`dir`](/en-US/docs/Web/HTML/Reference/Global_attributes/dir) attribute (`dir="rtl"`, `dir="ltr"`, or `dir="auto"`) and the [`<bdo>`](/en-US/docs/Web/HTML/Reference/Elements/bdo) element (or SVG's `direction` attribute).

> [!NOTE]
> Vertical text layout via the CSS [`writing-mode`](/en-US/docs/Web/CSS/Reference/Properties/writing-mode) property (such as `writing-mode: vertical-rl` for vertically typeset Japanese or Chinese ebooks) **is** permitted in EPUB CSS and is typically paired with `page-progression-direction="rtl"` on [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).

## Legacy `-epub-` prefixed properties

Before CSS Writing Modes, CSS Text Level 3, and CSS Text Decoration Level 3 reached maturity on the web, earlier versions of EPUB 3 defined `-epub-` vendor-prefixed equivalents so global typography features could be used reliably. EPUB 3.3 retains a subset of these prefixed properties for backward compatibility—such as `-epub-writing-mode`, `-epub-text-combine-horizontal`, `-epub-hyphens`, `-epub-line-break`, `-epub-word-break`, `-epub-text-align-last`, `-epub-text-emphasis-style`, `-epub-text-emphasis-color`, `-epub-text-emphasis-position`, and `-epub-text-underline-position`.

For new EPUB publications, **use the standard unprefixed CSS properties** (`writing-mode`, `text-combine-upright`, `hyphens`, `line-break`, `word-break`, `text-align-last`, `text-emphasis`, `text-underline-position`), as the W3C Publishing Working Group plans to drop `-epub-` prefixes in future major versions of EPUB.

## Examples

### Reflowable chapter stylesheet

The following example shows a clean `styles/main.css` stylesheet tailored for reflowable prose—using relative units, traditional book first-line paragraph indents, theme-safe borders via `currentColor`, and responsive figure sizing:

```css
/* Keep margins and typography relative to the reader's chosen font size */
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

/* Traditional book typography: indent consecutive paragraphs rather than spacing them */
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

/* Use currentColor borders instead of hardcoded colors so dark mode works cleanly */
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
