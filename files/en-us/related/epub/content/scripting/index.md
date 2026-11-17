---
title: EPUB scripting (JavaScript)
short-title: Scripting
slug: Related/EPUB/Content/Scripting
page-type: guide
sidebar: epub
---

EPUB 3 supports client-side [JavaScript](/en-US/docs/Web/JavaScript) inside [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) and [SVG](/en-US/docs/Related/EPUB/Content/SVG) content documents—for example, for interactive self-assessment quizzes in textbooks, clickable diagrams, or dynamic data visualizations.

> [!IMPORTANT]
> **Reading system support for JavaScript is optional and frequently disabled.** Many reflowable reading systems block scripts altogether for security, privacy, battery life, and pagination stability. Always treat JavaScript in EPUB as **progressive enhancement**: your publication must remain completely readable and understandable without any scripts running.

## Declaring scripted content in `<manifest>`

Two manifest rules apply whenever you use scripts or HTML forms in an EPUB:

1. **Declare external `.js` files in `<manifest>`**: Use the core media type `application/javascript` (or the core aliases `text/javascript` or `application/ecmascript`).
2. **Add `properties="scripted"` to scripted content documents**: Any XHTML or SVG `<item>` in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) that includes executable `<script>` elements (inline or external) **or HTML `<form>` elements** is a *scripted content document* and **must** include `scripted` in its `properties` attribute:

```xml
<manifest>
  <item id="quiz-js" href="scripts/quiz.js" media-type="application/javascript" />
  <item id="ch4" href="text/chapter-4.xhtml" media-type="application/xhtml+xml" properties="scripted" />
  <!-- other manifest items -->
</manifest>
```

Two edge cases are worth noting:

- **Non-executable data blocks**: A `<script>` element used strictly as an inert data block (such as `<script type="application/json">`) does *not* make a document scripted and does not require `properties="scripted"`.
- **Non-recursive `<iframe>` embedding**: `properties="scripted"` is not transitive—if an unscripted chapter (`chapter-4.xhtml`) embeds a scripted widget (`quiz-widget.xhtml`) via [`<iframe>`](/en-US/docs/Web/HTML/Reference/Elements/iframe), only `quiz-widget.xhtml` declares `properties="scripted"`.

## Scripting contexts

EPUB 3 defines two execution contexts for scripts, which carry very different compatibility trade-offs in reflowable ebooks:

### Container-constrained scripts (`<iframe>`)

A **container-constrained script** runs inside an XHTML or SVG document embedded in a parent XHTML page via an [`<iframe>`](/en-US/docs/Web/HTML/Reference/Elements/iframe) element of fixed dimensions.

- **Restrictions**: A container-constrained script **must not** modify the DOM of the parent document or resize its containing `<iframe>` rectangle.
- **Why this is preferred in reflowable books**: Because the interactive widget stays inside a fixed box, it cannot unexpectedly lengthen the chapter or break the reading system's dynamic page-column calculations. Reading systems are encouraged to support container-constrained scripts in reflowable books.

### Spine-level scripts

A **spine-level script** runs directly inside a top-level content document referenced in [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).

- Reading system support for spine-level scripts is mainly limited to fixed-layout publications or reflowable books rendered in continuous scrolling mode.
- In paginated reflowable mode, reading systems often disable spine-level scripts—or disable pagination on scripted chapters—because scripts that insert or expand DOM elements can push text off the bottom of a paginated column.

## Security, origin isolation, and input considerations

- **Unique origin per publication**: Reading systems assign each EPUB publication an isolated origin so scripts in one ebook cannot read storage or data belonging to another ebook. Avoid relying on `localStorage` or cookies persisting across sessions, as reading systems may clear or block persistent storage.
- **Network access**: Many reading systems run offline or block `fetch()` / `XMLHttpRequest` calls completely. If a script retrieves remote data, the host document's `<item>` in `<manifest>` must also declare `properties="remote-resources"`, and the script must obtain user consent before transmitting any user data.
- **Input portability**: E-readers run across touchscreens, e-ink tap zones (where horizontal swipes or edge taps may be intercepted by the reading system to turn pages!), mice, and assistive switches. Never rely on hover states or keyboard-only events alone, and ensure interactive controls are operable with standard click/activation events and WAI-ARIA attributes.
- **Escape `<` and `&` in inline scripts**: Because XHTML files are parsed as XML, operators like `i < 10` or `a && b` inside an inline `<script>` tag cause fatal XML parsing errors! Either place JavaScript in an external `.js` file (recommended) or wrap inline script code in `<![CDATA[ ... ]]>`.

## Detecting reading system capabilities (`navigator.epubReadingSystem`)

Supporting reading systems expose a [`navigator.epubReadingSystem`](https://www.w3.org/TR/epub-rs-33/#app-epubReadingSystem) object (`name`, `version`, `layoutStyle`, and `hasFeature()`) so scripts can check whether scripting or specific input events are supported before enhancing the UI. Always guard access to `navigator.epubReadingSystem` with a feature check, since browsers and non-supporting readers leave it `undefined`.

## Examples

### Progressive enhancement with an external script

The following example shows a self-check question in `text/quiz-widget.xhtml` (designed to be embedded via `<iframe>` or opened directly). When JavaScript is disabled, the answer remains accessible via native HTML `<details>` and `<summary>`; when JavaScript runs, `scripts/quiz.js` enhances the widget interactively without risking XML syntax errors from inline `<` or `&` characters:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Chapter 1 Self-Check</title>
    <link rel="stylesheet" type="text/css" href="../styles/main.css" />
    <script src="../scripts/quiz.js" defer="defer"></script>
  </head>
  <body>
    <section class="quiz-box" aria-labelledby="q1-prompt">
      <h2 id="q1-prompt">Quick check: From which port does the Pequod set sail?</h2>
      <details id="q1-answer">
        <summary>Show answer</summary>
        <p>Nantucket, Massachusetts.</p>
      </details>
    </section>
  </body>
</html>
```

## Specifications

- [EPUB 3.3: Scripting](https://www.w3.org/TR/epub-33/#sec-scripted-content)
- [EPUB 3.3: Scripting contexts](https://www.w3.org/TR/epub-33/#sec-scripted-context)
- [EPUB 3.3: Manifest `scripted` property](https://www.w3.org/TR/epub-33/#sec-scripted)
- [EPUB Reading Systems 3.3: `epubReadingSystem` object](https://www.w3.org/TR/epub-rs-33/#app-epubReadingSystem)

## See also

- [JavaScript](/en-US/docs/Web/JavaScript)
- [EPUB XHTML content documents](/en-US/docs/Related/EPUB/Content/XHTML)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
- [`<iframe>`: The Inline Frame element](/en-US/docs/Web/HTML/Reference/Elements/iframe)
