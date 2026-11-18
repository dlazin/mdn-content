---
title: Open Container Format
slug: Related/EPUB/Structure/OCF
page-type: guide
sidebar: epub
---

Open Container Format (OCF) is the ZIP packaging specification that bundles the files of an EPUB. Every OCF archive has a single root directory containing [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype), [`META-INF/container.xml`](/en-US/docs/Related/EPUB/Structure/META-INF), and the book's [package file](/en-US/docs/Related/EPUB/Package) and [publication content](/en-US/docs/Related/EPUB/Content) (usually grouped inside a [content directory](/en-US/docs/Related/EPUB/Structure/Content) such as `OEBPS`). Neither `mimetype` nor files in `META-INF` may be listed in the [manifest](/en-US/docs/Related/EPUB/Package/Manifest) or referenced as publication content.

## ZIP archive requirements

Renaming an ordinary `.zip` file to `.epub` does not produce a valid OCF archive. Packaging tools must follow these ZIP rules:

- Place [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype) first in the archive—uncompressed, unencrypted, and without extra fields in its ZIP header so `application/epub+zip` starts at byte offset 38.
- Store every archive entry either uncompressed or compressed with Deflate. Other compression algorithms are not allowed. Typically, text files use Deflate while already-compressed audio or video files are stored uncompressed for faster playback.
- Encode file and directory names in ZIP headers as UTF-8.
- Do not split archives across multiple ZIP disks or volumes.
- Use standard 32-bit ZIP headers unless the archive exceeds 4 GB or file-count limits, where ZIP64 (Version 1) extensions are allowed. Local file headers must set `version needed to extract` to `10`, `20` (for Deflate), or `45` (for ZIP64).
- Do not use password-protected ZIP encryption. Resource encryption and [font obfuscation](/en-US/docs/Related/EPUB/Content/Fonts#font_licensing_and_obfuscation) must be declared in [`META-INF/encryption.xml`](/en-US/docs/Related/EPUB/Structure/META-INF).

## File and path naming rules

Because an EPUB can be opened in diverse operating systems and e-readers, OCF restricts file and folder names:

- Paths are case-sensitive: if a file is stored as `images/Cover.jpg`, referencing `images/cover.jpg` fails on case-sensitive reading systems even if it worked on macOS or Windows during authoring. At the same time, two files in the same folder cannot have names that match after Unicode normalization and case-insensitive comparison (for example, `Chapter1.xhtml` and `chapter1.xhtml` cannot coexist in one directory).
- Each file or directory name must be at most 255 bytes in UTF-8, and the full path from the container root must be at most 65,535 bytes.
- Names must not end with a period (`.`) and must not contain `/` (reserved as the path separator), `\`, `:`, `*`, `?`, `"`, `<`, `>`, `|`, control characters (`U+0000`–`U+001F`, `U+007F`–`U+009F`), Unicode Private Use Area characters, or Unicode non-characters.
- Spaces are discouraged because they must be percent-encoded (`%20`) in URLs and can fail in older ZIP tools. Sticking to lowercase ASCII letters (`a`–`z`), digits (`0`–`9`), hyphens (`-`), underscores (`_`), and periods (`.`) avoids cross-platform encoding issues.

## Container URLs

Reading systems assign an internal root URL to the archive root so relative links resolve inside the book without exposing the device's filesystem. Consequently:

- Never start relative URLs with a leading slash (`/`, such as `/OEBPS/styles/main.css`); write paths relative to the current file (`../styles/main.css`). See [Relative paths across an EPUB](/en-US/docs/Related/EPUB/Structure/Content#relative_paths_across_an_epub) for examples.
- Never include extra `..` segments that climb past the archive root (such as `../../../../secret.xhtml`).
- Never use `file:` URLs anywhere in an EPUB.

## Inspecting EPUB files

An EPUB can be unzipped for inspection (for example, `unzip book.epub -d book-folder`).

When repackaging an unzipped folder, do not use the built-in "Compress" command in macOS Finder or Windows File Explorer. Desktop archive utilities compress `mimetype` and include hidden system files (such as `.DS_Store` or `Thumbs.db`) that are not listed in the [manifest](/en-US/docs/Related/EPUB/Package/Manifest).

Instead, repackage EPUB folders with a tool like [eCanCrusher](https://www.docdataflow.com/ecancrusher/) or, from a terminal inside the unzipped root folder, create the ZIP manually by adding `mimetype` first without compression (`-0`) or extra file attributes (`-X`), then adding `META-INF` and your [content directory](/en-US/docs/Related/EPUB/Structure/Content) while excluding hidden files:

```bash
zip -0 -X ../book.epub mimetype
zip -r -X ../book.epub META-INF OEBPS -x "*.DS_Store" -x "*Thumbs.db"
```

Afterwards, validate the archive with [EPUBCheck](https://www.w3.org/publishing/epubcheck/), the W3C's conformance checker.

## Specifications

- [EPUB 3.3: Open Container Format (OCF)](https://www.w3.org/TR/epub-33/#sec-ocf)
