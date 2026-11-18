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

## Declaring scripted content in the manifest

Declare external JavaScript files in the [manifest](/en-US/docs/Related/EPUB/Package/Manifest) with `media-type="application/javascript"` (or `text/javascript`), and add [`properties="scripted"`](/en-US/docs/Related/EPUB/Package/Manifest#manifest_item_properties) to any XHTML or SVG manifest item containing executable `<script>` elements or HTML `<form>` elements:

```xml
<manifest>
  <item id="quiz-js" href="scripts/quiz.js" media-type="application/javascript" />
  <item id="ch4" href="text/chapter-4.xhtml" media-type="application/xhtml+xml" properties="scripted" />
  <!-- other manifest items -->
</manifest>
```

Inert data blocks such as `<script type="application/json">` do not require `properties="scripted"`, and `properties="scripted"` is not transitive across `<iframe>` boundaries (only the scripted document itself declares `properties="scripted"`).

## Scripting contexts

EPUB 3 defines two execution contexts for scripts, with very different behavior in reflowable ebooks.

### Container-constrained scripts

A container-constrained script runs inside an XHTML or SVG document embedded in a parent XHTML page via a fixed-size [`<iframe>`](/en-US/docs/Web/HTML/Reference/Elements/iframe). It must not modify the parent document's DOM or resize its `<iframe>` box.

Because the widget stays inside fixed dimensions, it cannot change chapter length or break the reading system's dynamic page calculations. Reading systems that support JavaScript are encouraged to support container-constrained scripts in reflowable books.

### Spine-level scripts

A spine-level script runs directly inside a top-level content document listed in the [spine](/en-US/docs/Related/EPUB/Package/Spine). Support for spine-level scripts is mostly limited to fixed-layout books or reflowable books viewed in scrolling mode. In paginated reflowable mode, reading systems often disable spine-level scripts (or switch scripted chapters to scrolling) because DOM changes can push content past the bottom of a paginated column.

## Security, storage, and XML rules

- Reading systems assign each EPUB an isolated origin so scripts in one book cannot read data from another book. Do not assume `localStorage` or cookies persist across sessions.
- Many reading systems run offline or block `fetch()` and `XMLHttpRequest`. If a script loads remote data, flag the host document's manifest item with [`properties="remote-resources"`](/en-US/docs/Related/EPUB/Package/Manifest#manifest_item_properties), and obtain user consent before sending any user data.
- Design controls for touchscreens, e-ink tap zones (where edge taps or horizontal swipes often turn pages), mice, and assistive switches. Do not rely on hover states alone.
- Put JavaScript in an external script file (or wrap inline code in `<![CDATA[ ... ]]>`) so `<` and `&` operators in `i < 10` or `a && b` do not cause [XML parsing errors](/en-US/docs/Related/EPUB/Content/XHTML#xml_syntax_rules).

## Detecting reading system capabilities

Supporting reading systems expose [`navigator.epubReadingSystem`](https://www.w3.org/TR/epub-rs-33/#app-epubReadingSystem) so scripts can call `hasFeature()` (for example, `navigator.epubReadingSystem.hasFeature("dom-manipulation")`) before modifying the page. Always check that `navigator.epubReadingSystem` is defined before calling it, because browsers and non-supporting readers leave it `undefined`.

> [!NOTE]
> The `name`, `version`, and `layoutStyle` properties on `navigator.epubReadingSystem` are deprecated in EPUB 3.3 due to fingerprinting concerns and inconsistent values across reading apps. Prefer `hasFeature()` checks instead.

## Examples

### Progressive enhancement with an external script

In this `text/quiz-widget.xhtml` example, readers can reveal the answer with native HTML `<details>` and `<summary>` when JavaScript is disabled, while `scripts/quiz.js` enhances the widget when scripts run:

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
