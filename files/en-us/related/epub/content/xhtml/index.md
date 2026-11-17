---
title: EPUB XHTML content documents
short-title: XHTML
slug: Related/EPUB/Content/XHTML
page-type: guide
sidebar: epub
---

In EPUB 3, the chapters and text sections of a book are written as **[XHTML](/en-US/docs/Glossary/XHTML) content documents**—standard [HTML](/en-US/docs/Web/HTML) documents written using strict [XML](/en-US/docs/Web/XML/Guides/XML_introduction) syntax rules and declared in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) with `media-type="application/xhtml+xml"`.

Most reflowable ebooks divide the book into one `.xhtml` file per chapter or major section (such as `cover.xhtml`, `copyright.xhtml`, `chapter-1.xhtml`, and `chapter-2.xhtml`) and list those files in reading order in the package [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine). Splitting a book by chapter helps e-readers load chapters quickly even on low-memory devices and ensures each chapter starts cleanly on a new screen page.

## XML syntax requirements

Unlike web browsers rendering `text/html` pages—which silently repair unclosed tags or unescaped ampersands—EPUB reading systems parse `.xhtml` files with a strict XML parser. Even a single XML syntax error causes the reading system to halt rendering and display a fatal error banner instead of the chapter.

Every XHTML content document must follow XML well-formedness rules:

- **Declare the XHTML namespace**: The root `<html>` element must include `xmlns="http://www.w3.org/1999/xhtml"`. Include both `xml:lang` and `lang` on `<html>` to declare the document language, and declare `xmlns:epub="http://www.idpf.org/2007/ops"` whenever the document uses attributes like [`epub:type`](/en-US/docs/Related/EPUB/Content/epub:type).
- **Close every element**: Void HTML elements such as `<img>`, `<meta>`, `<link>`, `<br>`, `<hr>`, and `<source>` must be self-closed (`<br />`, `<img src="..." alt="..." />`). Non-void elements such as `<p>` and `<li>` must always have matching closing tags (`</p>`, `</li>`).
- **Quote all attributes and expand boolean attributes**: Every attribute value must be enclosed in quotes (`id="ch1"`). Bare HTML boolean attributes such as `hidden` or `controls` are invalid XML; write `hidden=""` (or `hidden="hidden"`) and `controls="controls"`.
- **Use lowercase element and attribute names**: XML is case-sensitive, so `<P>` or `<DIV>` does not match `<p>` or `<div>` in the XHTML namespace.
- **Escape `&` and `<` in text and URLs**: Write raw ampersands as `&amp;` (including inside query strings in `href` URLs) and literal less-than signs as `&lt;`.
- **Avoid HTML named character entities**: XML only predefines five named entities (`&amp;`, `&lt;`, `&gt;`, `&quot;`, and `&apos;`). Named HTML entities such as `&nbsp;`, `&mdash;`, or `&copy;` cause a fatal XML undefined-entity error in EPUB 3. Enter Unicode characters directly in UTF-8 (`—`, `©`) or use numeric character references (`&#160;` for a non-breaking space, `&#8212;` for an em dash).

## Embedded MathML

XHTML content documents in EPUB 3 can include inline or block mathematical notation using [MathML](/en-US/docs/Web/MathML):

```xml
<p>
  The Pythagorean theorem states that
  <math xmlns="http://www.w3.org/1998/Math/MathML" display="inline">
    <msup><mi>a</mi><mn>2</mn></msup>
    <mo>+</mo>
    <msup><mi>b</mi><mn>2</mn></msup>
    <mo>=</mo>
    <msup><mi>c</mi><mn>2</mn></msup>
  </math>.
</p>
```

When embedding MathML in an EPUB XHTML document, follow two rules:

1. **Declare `properties="mathml"` in `<manifest>`**: Any XHTML `<item>` in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) that contains `<math>` markup **must** include the `mathml` token in its `properties` attribute:

   ```xml
   <item id="ch2" href="text/chapter-2.xhtml" media-type="application/xhtml+xml" properties="mathml" />
   ```

2. **Use Presentation MathML**: Direct children of `<math>` must use Presentation MathML elements (`<mrow>`, `<mi>`, `<mn>`, `<mo>`, `<mfrac>`, `<msup>`, etc.). Content MathML is only permitted inside an `<annotation-xml>` child of a `<semantics>` element (with `encoding="MathML-Content"` or `encoding="application/mathml-content+xml"` and `name="contentequiv"`).

## EPUB extensions and discouraged HTML elements

Aside from XML syntax rules, XHTML content documents support the full HTML standard with a few EPUB-specific additions and constraints:

- **Allowed EPUB extensions**: You can annotate elements with [`epub:type`](/en-US/docs/Related/EPUB/Content/epub:type) (except on `<head>` and metadata elements), `epub:prefix`, and RDFa attributes (in addition to native HTML microdata and JSON-LD). Two older EPUB 3.0 elements—`<epub:switch>` and `<epub:trigger>`—are deprecated and should not be used.
- **Discouraged HTML elements**:
  - `<base>`: Avoid using `<base>`, because setting a document base URL can cause reading systems to misinterpret relative internal links as remote web resources.
  - `<rp>`: Avoid ruby fallback parentheses (`<rp>`) inside `<ruby>` annotations, since EPUB 3 reading systems natively support ruby rendering.
  - `<embed>`: Prefer [`<object>`](/en-US/docs/Web/HTML/Reference/Elements/object) over `<embed>`, because `<object>` provides built-in fallback markup when a reading system does not support scripting or a foreign media type.

## Examples

### Minimal XHTML chapter document

The following example shows a complete, valid XHTML chapter file ready to be declared in `<manifest>` and referenced in `<spine>`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:epub="http://www.idpf.org/2007/ops"
      xml:lang="en"
      lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Chapter 1: Loomings</title>
    <link rel="stylesheet" type="text/css" href="../styles/main.css" />
  </head>
  <body>
    <section epub:type="bodymatter chapter" role="doc-chapter" aria-labelledby="ch1-heading">
      <h1 id="ch1-heading">Chapter 1: Loomings</h1>
      <p>
        Call me Ishmael. Some years ago—never mind how long precisely—having little or no
        money in my purse, and nothing particular to interest me on shore, I thought I would
        sail about a little and see the watery part of the world.
      </p>
    </section>
  </body>
</html>
```

## Specifications

- [EPUB 3.3: XHTML content documents](https://www.w3.org/TR/epub-33/#sec-xhtml)
- [EPUB 3.3: HTML extensions](https://www.w3.org/TR/epub-33/#sec-xhtml-extensions)
- [EPUB 3.3: HTML deviations and constraints](https://www.w3.org/TR/epub-33/#sec-xhtml-deviations)
- [EPUB 3.3: Embedded MathML](https://www.w3.org/TR/epub-33/#sec-xhtml-mathml)

## See also

- [EPUB publication content overview](/en-US/docs/Related/EPUB/Content)
- [XHTML glossary entry](/en-US/docs/Glossary/XHTML)
- [EPUB `epub:type` attribute](/en-US/docs/Related/EPUB/Content/epub:type)
- [MathML](/en-US/docs/Web/MathML)
