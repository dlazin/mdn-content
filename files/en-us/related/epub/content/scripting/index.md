---
title: EPUB scripting (JavaScript)
short-title: Scripting
slug: Related/EPUB/Content/Scripting
page-type: guide
sidebar: epub
---

EPUB 3 supports client-side [JavaScript](/en-US/docs/Web/JavaScript) in [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) and [SVG](/en-US/docs/Related/EPUB/Content/SVG) content documents—for example, for textbook self-check quizzes, interactive diagrams, or data visualizations.

> [!IMPORTANT]
> JavaScript support in EPUB reading systems is optional and often disabled for security, privacy, battery life, and pagination stability. Treat JavaScript as progressive enhancement: your book must remain readable and understandable when scripts do not run.

## Declaring scripted content in `<manifest>`

Follow two rules whenever an EPUB includes scripts or HTML forms:

- Declare external `.js` files in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) with `media-type="application/javascript"` (or the core aliases `text/javascript` or `application/ecmascript`).
- Add `properties="scripted"` to any XHTML or SVG `<item>` in `<manifest>` that contains executable `<script>` elements (inline or external) or HTML `<form>` elements:

```xml
<manifest>
  <item id="quiz-js" href="scripts/quiz.js" media-type="application/javascript" />
  <item id="ch4" href="text/chapter-4.xhtml" media-type="application/xhtml+xml" properties="scripted" />
  <!-- other manifest items -->
</manifest>
```

Two cases do not require `properties="scripted"`:

- Non-executable data blocks such as `<script type="application/json">`.
- Parent documents that embed a scripted widget via [`<iframe>`](/en-US/docs/Web/HTML/Reference/Elements/iframe): `properties="scripted"` is not transitive, so only the embedded widget's `<item>` declares `properties="scripted"`.

## Scripting contexts

EPUB 3 defines two execution contexts for scripts, with very different behavior in reflowable ebooks.

### Container-constrained scripts (`<iframe>`)

A container-constrained script runs inside an XHTML or SVG document embedded in a parent XHTML page via a fixed-size [`<iframe>`](/en-US/docs/Web/HTML/Reference/Elements/iframe). It must not modify the parent document's DOM or resize its `<iframe>` box.

Because the widget stays inside fixed dimensions, it cannot change chapter length or break the reading system's dynamic page calculations. Reading systems that support JavaScript are encouraged to support container-constrained scripts in reflowable books.

### Spine-level scripts

A spine-level script runs directly inside a top-level content document listed in [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine). Support for spine-level scripts is mostly limited to fixed-layout books or reflowable books viewed in scrolling mode. In paginated reflowable mode, reading systems often disable spine-level scripts (or switch scripted chapters to scrolling) because DOM changes can push content past the bottom of a paginated column.

## Security, storage, and XML rules

- Reading systems assign each EPUB an isolated origin so scripts in one book cannot read data from another book. Do not assume `localStorage` or cookies persist across sessions.
- Many reading systems run offline or block `fetch()` and `XMLHttpRequest`. If a script loads remote data, the host document's `<item>` in `<manifest>` must also declare `properties="remote-resources"`, and the script must obtain user consent before sending any user data.
- Design controls for touchscreens, e-ink tap zones (where edge taps or horizontal swipes often turn pages), mice, and assistive switches. Do not rely on hover states alone.
- Because XHTML is parsed as XML, characters like `<` and `&` in `i < 10` or `a && b` inside an inline `<script>` element cause fatal XML parsing errors. Put JavaScript in an external `.js` file (recommended) or wrap inline code in `<![CDATA[ ... ]]>`.

## Detecting reading system capabilities

Supporting reading systems expose [`navigator.epubReadingSystem`](https://www.w3.org/TR/epub-rs-33/#app-epubReadingSystem) so scripts can call `hasFeature()` (for example, `navigator.epubReadingSystem.hasFeature("dom-manipulation")`) before modifying the page. Always check that `navigator.epubReadingSystem` is defined before calling it, because browsers and non-supporting readers leave it `undefined`.

> [!NOTE]
> The `name`, `version`, and `layoutStyle` properties on `navigator.epubReadingSystem` are deprecated in EPUB 3.3 due to fingerprinting concerns and inconsistent values across reading apps. Prefer `hasFeature()` checks instead.

## Examples

### Progressive enhancement with an external script

In this `text/quiz-widget.xhtml` example, readers can reveal the answer with native HTML `<details>` and `<summary>` when JavaScript is disabled. When scripts are enabled, `scripts/quiz.js` enhances the widget without risking XML syntax errors from inline `<` or `&` operators:

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
