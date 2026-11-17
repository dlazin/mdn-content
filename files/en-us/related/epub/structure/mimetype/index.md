---
title: EPUB mimetype file
short-title: mimetype
slug: Related/EPUB/Structure/mimetype
page-type: guide
sidebar: epub
---

Every EPUB contains an identical `mimetype` file in the root of the container. Its sole contents are the following ASCII string:

```plain
application/epub+zip
```

This file tells reading systems and operating systems that the ZIP container is an EPUB rather than a generic archive of compressed files.

## Purpose

An EPUB is packaged as a ZIP archive using the [Open Container Format (OCF)](/en-US/docs/Related/EPUB/Structure/OCF). Because a ZIP archive can hold any kind of data, a system cannot tell from the ZIP header alone that the archive is an ebook.

To solve this without relying on the `.epub` file extension or unzipping the entire archive, the EPUB specification requires that the first file in the ZIP container be an uncompressed `mimetype` file. This places the above string `application/epub+zip` at a fixed byte offset (starting at byte 38 of the EPUB file), letting software identify the file by reading its first few dozen bytes.

## Requirements

Every EPUB `mimetype` file must follow strict formatting and packaging rules:

- The file must be named `mimetype` (all lowercase, with no file extension) and be in the root directory of the EPUB container.
- The file must contain only the 20-byte ASCII string `application/epub+zip`. It must not contain any leading or trailing whitespace, newlines, or a byte order mark.
- When creating the ZIP container, `mimetype` must be the first file added.
- The file must be stored uncompressed (using the ZIP `STORE` method rather than `DEFLATE`) and unencrypted so that it remains plain text in the archive header.
- The ZIP header for `mimetype` must not include extra fields that would shift the `application/epub+zip` string away from byte offset 38.

> [!NOTE]
> If you package an EPUB from the command line using `zip`, add the `mimetype` file first with compression disabled (`-0`) and extra file attributes excluded (`-X`) before adding the remaining directories:
>
> ```bash
> zip -0 -X book.epub mimetype
> zip -r -X book.epub META-INF OEBPS
> ```

## Specifications

- [EPUB 3.3: OCF ZIP container media type identification](https://www.w3.org/TR/epub-33/#sec-zip-container-mime)

## See also

- [EPUB structure](/en-US/docs/Related/EPUB/Structure)
- [Open Container Format (OCF)](/en-US/docs/Related/EPUB/Structure/OCF)
- [`META-INF` directory](/en-US/docs/Related/EPUB/Structure/META-INF)
- [Common MIME types](/en-US/docs/Web/HTTP/Guides/MIME_types/Common_types)
