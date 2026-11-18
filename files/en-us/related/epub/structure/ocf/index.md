---
title: Open Container Format
slug: Related/EPUB/Structure/OCF
page-type: guide
sidebar: epub
---

The **Open Container Format (OCF)** is the ZIP packaging specification that bundles the files of an EPUB into a single `.epub` archive. For an overview of the top-level files and folders inside an `.epub` archive, see [EPUB structure](/en-US/docs/Related/EPUB/Structure).

Every OCF archive has a single root directory containing [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype), [`META-INF/container.xml`](/en-US/docs/Related/EPUB/Structure/META-INF), and the book's [package file](/en-US/docs/Related/EPUB/Package) and [publication content](/en-US/docs/Related/EPUB/Content) (usually grouped inside a [content directory](/en-US/docs/Related/EPUB/Structure/Content) such as `OEBPS/`). Neither `mimetype` nor files in `META-INF/` may be listed in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) or referenced as publication content.

## ZIP archive requirements

Renaming an ordinary `.zip` file to `.epub` does not produce a valid OCF archive. Packaging tools must follow these ZIP rules:

- Place [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype) first in the archive, stored uncompressed (`STORE`, method `0`), unencrypted, and without extra fields in its local ZIP header so `application/epub+zip` starts at byte offset 38.
- Store every archive entry either uncompressed (`STORE`, method `0`) or compressed with Deflate (method `8`). Other compression algorithms such as BZip2, LZMA, or Zstandard are not allowed. Text files (XML, XHTML, CSS, SVG) typically use Deflate, while already-compressed audio or video files can be stored uncompressed for faster playback.
- Encode file and directory names in ZIP headers as UTF-8.
- Do not split archives across multiple ZIP disks or volumes.
- Use standard 32-bit ZIP headers unless the archive exceeds 4 GB or file-count limits, where ZIP64 (Version 1) extensions are allowed. Local file headers must set `version needed to extract` to `10`, `20` (for Deflate), or `45` (for ZIP64).
- Do not use password-protected ZIP encryption. Resource encryption and [font obfuscation](/en-US/docs/Related/EPUB/Content/Fonts#font_licensing_and_obfuscation) must be declared in [`META-INF/encryption.xml`](/en-US/docs/Related/EPUB/Structure/META-INF), and `mimetype`, files in `META-INF/`, and the `.opf` [package file](/en-US/docs/Related/EPUB/Package) must remain unencrypted.

## File and path naming rules

Because an `.epub` file may be opened across Linux, macOS, Windows, Android, iOS, and dedicated e-readers, OCF restricts file and folder names:

- Paths are case-sensitive: if a file is stored as `images/Cover.jpg`, referencing `images/cover.jpg` fails on case-sensitive reading systems even if it worked on macOS or Windows during authoring. At the same time, two files in the same folder cannot have names that match after Unicode normalization and case-insensitive comparison (for example, `Chapter1.xhtml` and `chapter1.xhtml` cannot coexist in one directory).
- Each file or directory name must be at most 255 bytes in UTF-8, and the full path from the container root must be at most 65,535 bytes.
- Names must not end with a period (`.`) and must not contain `/` (reserved as the path separator), `\`, `:`, `*`, `?`, `"`, `<`, `>`, `|`, control characters (`U+0000`–`U+001F`, `U+007F`–`U+009F`), Unicode Private Use Area characters, or Unicode non-characters.
- Spaces are discouraged because they must be percent-encoded (`%20`) in URLs and can fail in older ZIP tools. Sticking to lowercase ASCII letters (`a`–`z`), digits (`0`–`9`), hyphens (`-`), underscores (`_`), and periods (`.`) avoids cross-platform encoding issues.

## Container URLs

Reading systems assign an internal root URL to the archive root so relative links resolve inside the book without exposing the device's filesystem:

- Never start relative URLs with a leading slash (`/`, such as `/OEBPS/styles/main.css`); write paths relative to the current file (`../styles/main.css`). See [Relative paths across an EPUB](/en-US/docs/Related/EPUB/Structure/Content#relative_paths_across_an_epub) for examples.
- Never include extra `..` segments that climb past the archive root (such as `../../../../secret.xhtml`).
- Never use `file:` URLs anywhere in an EPUB.

## Packaging and inspecting `.epub` files

You can inspect any unencrypted `.epub` file by unzipping it (for example, `unzip book.epub -d book-folder`).

When re-packaging an unzipped folder, do not use the built-in "Compress" command in macOS Finder or Windows File Explorer. Desktop archive utilities compress `mimetype`, add files out of order, and include hidden system files (such as `.DS_Store` or `Thumbs.db`) that are not listed in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest).

From a terminal inside the unzipped root folder, add `mimetype` first with compression (`-0`) and extra file attributes (`-X`) turned off, then add `META-INF` and your [content directory](/en-US/docs/Related/EPUB/Structure/Content) while excluding hidden files:

```bash
zip -0 -X ../book.epub mimetype
zip -r -X ../book.epub META-INF OEBPS -x "*.DS_Store" -x "*Thumbs.db"
```

You can also package folders with EPUB tools such as Sigil, Calibre, or eCanCrusher, then validate the archive with [EPUBCheck](https://www.w3.org/publishing/epubcheck/), the W3C's conformance checker.

## Specifications

- [EPUB 3.3: Open Container Format (OCF)](https://www.w3.org/TR/epub-33/#sec-ocf)
- [EPUB 3.3: File and directory structure](https://www.w3.org/TR/epub-33/#sec-container-file-and-dir-structure)
- [EPUB 3.3: File paths and file names](https://www.w3.org/TR/epub-33/#sec-container-filenames)
- [EPUB 3.3: URLs in the OCF abstract container](https://www.w3.org/TR/epub-33/#sec-container-iri)
- [EPUB 3.3: OCF ZIP container](https://www.w3.org/TR/epub-33/#sec-container-zip)

## See also

- [EPUB structure](/en-US/docs/Related/EPUB/Structure)
- [EPUB `mimetype` file](/en-US/docs/Related/EPUB/Structure/mimetype)
- [EPUB `META-INF` directory](/en-US/docs/Related/EPUB/Structure/META-INF)
- [EPUB content directory](/en-US/docs/Related/EPUB/Structure/Content)
