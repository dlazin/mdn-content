---
title: EPUB XHTML content documents
short-title: XHTML
slug: Related/EPUB/Reference/Content/XHTML
page-type: guide
sidebar: epub
---

In EPUB 3, chapters and text sections are **[XHTML](/en-US/docs/Glossary/XHTML) content documents**: [HTML](/en-US/docs/Web/HTML) files written in [XML](/en-US/docs/Web/XML/Guides/XML_introduction) syntax and declared in the [manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest) with `media-type="application/xhtml+xml"`.

Most reflowable ebooks use one XHTML file per chapter or section (such as `cover.xhtml`, `copyright.xhtml`, `chapter-1.xhtml`, and `chapter-2.xhtml`) and list them in order in the [spine](/en-US/docs/Related/EPUB/Reference/Package/Spine). Splitting a book into chapter files helps low-memory e-readers load pages quickly and starts each chapter on a new screen page.

## XML syntax rules

Unlike web browsers parsing `text/html`, EPUB reading systems parse XHTML files with a strict XML parser. A single XML syntax error stops the chapter from rendering and displays an error message instead.

Every XHTML content document must follow XML well-formedness rules:

- Encode files in UTF-8 (or UTF-16, with UTF-8 recommended). Including `<?xml version="1.0" encoding="UTF-8"?>` at the top and `<!DOCTYPE html>` before `<html>` is standard practice, though both are optional in UTF-8 XML.
- Set `xmlns="http://www.w3.org/1999/xhtml"` on `<html>`. Include both `xml:lang` and `lang` for the document language, and add `xmlns:epub="http://www.idpf.org/2007/ops"` whenever the file uses [`epub:type`](/en-US/docs/Related/EPUB/Reference/Content/epub:type).
- Close every element. Void HTML elements (`<img>`, `<meta>`, `<link>`, `<br>`, `<hr>`, `<source>`) must be self-closed (`<br />`), and non-void elements (`<p>`, `<li>`) must have closing tags (`</p>`, `</li>`).
- Quote all attribute values (`id="ch1"`) and write boolean attributes in full XML form (`hidden=""` or `controls="controls"`, never bare `hidden`).
- Use lowercase element and attribute names (`<p>`, not `<P>`).
- Escape raw `&` and `<` characters as `&amp;` and `&lt;`, including `&` inside URL query strings.
- Do not use HTML named character entities such as `&nbsp;`, `&mdash;`, or `&copy;`. XML only defines `&amp;`, `&lt;`, `&gt;`, `&quot;`, and `&apos;`, so write Unicode characters directly in UTF-8 (`—`, `©`) or use numeric references (`&#160;` for a non-breaking space).

## Embedded MathML

XHTML content documents can include mathematical notation using [MathML](/en-US/docs/Web/MathML):

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

Follow three rules when including `<math>` elements:

- Add `properties="mathml"` to the XHTML file's item in the [manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest):

  ```xml
  <item id="ch2" href="text/chapter-2.xhtml" media-type="application/xhtml+xml" properties="mathml" />
  ```

- Use Presentation MathML (`<mrow>`, `<mi>`, `<mn>`, `<mo>`, `<mfrac>`, `<msup>`, and related elements) for `<math>` children. Content MathML is allowed only inside `<annotation-xml>` within `<semantics>` (with `encoding="MathML-Content"` or `encoding="application/mathml-content+xml"` and `name="contentequiv"`).
- Avoid adding `alttext` or `aria-label` to `<math>` elements; assistive technologies navigate Presentation MathML directly, and extra text labels can cause equations to be read twice or prevent term-by-term exploration.

## EPUB extensions and discouraged HTML elements

XHTML content documents support standard HTML along with [`epub:type`](/en-US/docs/Related/EPUB/Reference/Content/epub:type) (allowed on `<body>` and its descendants, not inside `<head>`), `epub:prefix`, and RDFa attributes. Two early EPUB 3 elements—`<epub:switch>` and `<epub:trigger>`—are deprecated and should not be used.

EPUB 3.3 also discourages three HTML elements:

- `<base>`, because changing the document base URL can break internal relative links or cause reading systems to treat internal paths as remote resources.
- `<rp>`, because EPUB 3 reading systems support `<ruby>` annotations natively without fallback parentheses.
- `<embed>` for scripted resources, because [`<object>`](/en-US/docs/Web/HTML/Reference/Elements/object) provides built-in fallback content when scripting is unavailable.

## Examples

### Minimal XHTML chapter

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

- [EPUB publication content](/en-US/docs/Related/EPUB/Reference/Content)
- [XHTML](/en-US/docs/Glossary/XHTML)
- [EPUB `epub:type` attribute](/en-US/docs/Related/EPUB/Reference/Content/epub:type)
- [MathML](/en-US/docs/Web/MathML)
