---
title: Open Container Format
slug: Related/EPUB/Structure/OCF
page-type: guide
sidebar: epub
---

The **Open Container Format (OCF)** is the ZIP-based packaging standard that bundles all the files of an EPUB publication into a single `.epub` file. OCF defines two layers:

- The **abstract container**: A virtual directory tree with a single root directory, a required [`META-INF`](/en-US/docs/Related/EPUB/Structure/META-INF) configuration directory, and rules for file naming and relative URLs.
- The **ZIP physical container**: The specific ZIP archive constraints—including placing an uncompressed [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype) file first—that turn that folder tree into a valid `.epub` file.

## Required container layout

Every OCF container has a single common root directory:

```plain
book.epub/
├── mimetype
├── META-INF/
│   └── container.xml
└── OEBPS/
    ├── package.opf
    ├── nav.xhtml
    └── ...
```

- **[`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype)**: Reserved in the root directory to identify the ZIP archive as `application/epub+zip`.
- **[`META-INF`](/en-US/docs/Related/EPUB/Structure/META-INF)**: Required as a direct child of the root directory. It must contain `container.xml` (which points to the [package file](/en-US/docs/Related/EPUB/Package)) and may include optional container files such as `encryption.xml` or `signatures.xml`.
- **Publication resources**: The [package file](/en-US/docs/Related/EPUB/Package) and all [publication content](/en-US/docs/Related/EPUB/Content) files can live anywhere under the root directory outside `META-INF`, though placing them together inside a [content directory](/en-US/docs/Related/EPUB/Structure/Content) (such as `OEBPS/`, `OPS/`, or `EPUB/`) is strongly recommended. Neither `mimetype` nor any file inside `META-INF` may be listed in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) or referenced from content documents.

## ZIP archive requirements

Not every `.zip` file renamed to `.epub` is a valid OCF container. When creating an `.epub` archive, packaging tools must satisfy the following ZIP constraints:

- **`mimetype` first and uncompressed**: The [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype) file must be the very first entry in the ZIP archive, stored uncompressed (`STORE`, compression method `0`), unencrypted, with no extra fields in its local ZIP header so `application/epub+zip` begins at byte offset 38.
- **Allowed compression methods**: Every entry in the archive must be either uncompressed (`STORE`, method `0`) or compressed with **Deflate** (method `8`). Other ZIP compression algorithms (such as BZip2, LZMA, or Zstandard) are prohibited. Text-based files (XML, XHTML, CSS, SVG) are normally Deflate-compressed, while already-compressed binary media (such as large audio or video streams) can be stored uncompressed to avoid playback overhead.
- **UTF-8 filenames**: All file and directory names in the ZIP archive headers must be encoded in UTF-8.
- **Single-file archive**: Multi-disk or split/spanned ZIP archives are prohibited.
- **ZIP64 extensions**: Standard 32-bit ZIP headers should be used unless the publication's size or file count exceeds 4 GB limits, in which case ZIP64 (Version 1) extensions are allowed. In local file headers, the `version needed to extract` field must match the feature level used (`10`, `20` for Deflate, or `45` for ZIP64).
- **No ZIP-layer encryption**: Built-in ZIP password encryption is prohibited. Any resource encryption or [font obfuscation](/en-US/docs/Related/EPUB/Content/Fonts#font_licensing_and_obfuscation) must be described in [`META-INF/encryption.xml`](/en-US/docs/Related/EPUB/Structure/META-INF), and `mimetype`, files in `META-INF/`, and the `.opf` [package file](/en-US/docs/Related/EPUB/Package) must never be encrypted.

## File and path naming rules

Because an `.epub` file may be unpacked or read across Linux, macOS, Windows, Android, iOS, and embedded e-reader firmware, OCF enforces strict cross-platform path rules:

- **Case sensitivity and uniqueness**: Paths inside an EPUB are **case-sensitive**—if a file is stored as `images/Cover.jpg`, referencing `images/cover.jpg` in XHTML or `<manifest>` will fail on case-sensitive reading systems, even if it appeared to work on macOS or Windows during authoring. At the same time, two files in the same directory must not have names that collide after Unicode canonical normalization and case folding (for example, `Chapter1.xhtml` and `chapter1.xhtml` cannot coexist in the same folder).
- **Byte-length limits**: Each individual file or directory name must not exceed **255 bytes** in UTF-8, and the full container path from the root must not exceed **65,535 bytes**.
- **Prohibited characters**: File and directory names must not contain:
  - `/` (`U+002F`, reserved strictly as the directory separator), `\`, `:`, `*`, `?`, `"`, `<`, `>`, or `|`
  - A period (`.`, `U+002E`) as the **last** character of a file or directory name
  - ASCII control characters (`U+0000`–`U+001F` and `U+007F`), C1 control characters (`U+0080`–`U+009F`), Unicode Private Use Area characters, or Unicode non-characters
- **Avoid spaces and special characters**: Although OCF permits Unicode characters in filenames, spaces (`U+0020`) are discouraged because they must be percent-encoded (`%20`) in URL attributes (`href` and `src`) and can trip up older ZIP utilities. Using lowercase ASCII letters (`a`–`z`), digits (`0`–`9`), hyphens (`-`), underscores (`_`), and periods (`.`) avoids cross-platform encoding bugs.

## Container URLs and security boundaries

Reading systems assign an internal **container root URL** to the root directory of the OCF container so browser engines can resolve relative links between files without exposing the user's host filesystem:

- **No leading slashes**: Relative URLs inside an EPUB must not begin with `/` (for example, write `../styles/main.css` rather than `/OEBPS/styles/main.css`).
- **No escaping the container root**: Relative paths must not contain extra `..` segments that climb above the container root directory (such as `../../../../secret.xhtml`). Conformance checkers reject out-of-container relative paths as errors.
- **No `file:` URLs**: `file://` URLs are prohibited everywhere in an EPUB publication.

## Packaging and inspecting `.epub` files

Because an `.epub` file is a ZIP archive, you can inspect any unencrypted ebook by unzipping it (for example, with `unzip book.epub -d book-folder`, or by renaming a copy to `.zip`).

When re-packaging an unzipped folder into an `.epub`, however, double-clicking "Compress" in macOS Finder or Windows File Explorer will **not** produce a valid EPUB, because desktop archive utilities compress `mimetype`, add it in arbitrary order, or bundle hidden OS metadata files (such as `.DS_Store` or `Thumbs.db`) that are not declared in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest).

From a terminal inside the unzipped root folder, add `mimetype` first with compression (`-0`) and extra attributes (`-X`) disabled, then add `META-INF` and your [content directory](/en-US/docs/Related/EPUB/Structure/Content) while excluding hidden system files:

```bash
zip -0 -X ../book.epub mimetype
zip -r -X ../book.epub META-INF OEBPS -x "*.DS_Store" -x "*Thumbs.db"
```

Alternatively, use an EPUB-aware packaging utility (such as Sigil, Calibre, or eCanCrusher), and verify the finished archive with [EPUBCheck](https://www.w3.org/publishing/epubcheck/).

## Specifications

- [EPUB 3.3: Open Container Format (OCF)](https://www.w3.org/TR/epub-33/#sec-ocf)
- [EPUB 3.3: File and directory structure](https://www.w3.org/TR/epub-33/#sec-container-file-and-dir-structure)
- [EPUB 3.3: File paths and file names](https://www.w3.org/TR/epub-33/#sec-container-filenames)
- [EPUB 3.3: URLs in the OCF abstract container](https://www.w3.org/TR/epub-33/#sec-container-iri)
- [EPUB 3.3: OCF ZIP container](https://www.w3.org/TR/epub-33/#sec-container-zip)

## See also

- [EPUB structure](/en-US/docs/Related/EPUB/Structure)
- [The EPUB `mimetype` file](/en-US/docs/Related/EPUB/Structure/mimetype)
- [The EPUB `META-INF` directory](/en-US/docs/Related/EPUB/Structure/META-INF)
- [EPUB content directory](/en-US/docs/Related/EPUB/Structure/Content)
