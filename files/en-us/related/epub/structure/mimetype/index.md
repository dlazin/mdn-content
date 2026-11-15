---
title: The EPUB mimetype file
short-title: mimetype
slug: Related/EPUB/Structure/mimetype
page-type: guide
tags:
  - EPUB
  - Ebook
  - Ebooks
  - Reference
---
{{EPUBRef}}

Every EPUB contains an identical **`mimetype`** file in the root directory of the container. Its sole contents are the ASCII string `application/epub+zip`:

```plain
application/epub+zip
```

The `mimetype` file tells reading systems and operating systems that the ZIP container is an EPUB publication rather than a generic archive of compressed files.

## Purpose

An EPUB publication is packaged as a ZIP archive using the [Open Container Format (OCF)](/en-US/docs/Related/EPUB/Structure/OCF). Because a ZIP archive can hold any kind of data, a reading system or operating system inspecting the file cannot tell from the standard ZIP header alone that the archive is an ebook.

To solve this without relying on the `.epub` file extension or unzipping the entire archive, the EPUB specification requires that an uncompressed `mimetype` file be placed as the very first file in the ZIP container. This places the string `application/epub+zip` at a fixed byte offset (starting at byte 38 of the EPUB file), allowing software to identify the file as an EPUB by reading its first few dozen bytes.

## Requirements

Every EPUB `mimetype` file must follow strict formatting and packaging rules:

- **Filename and location**: The file must be named `mimetype` (all lowercase, with no file extension) and placed in the root directory of the EPUB container. It must not be placed inside the [`META-INF`](/en-US/docs/Related/EPUB/Structure/META-INF) directory or the [content directory](/en-US/docs/Related/EPUB/Structure/Content).
- **Exact contents**: The file must contain only the 20-byte ASCII string `application/epub+zip`. It must not contain any leading or trailing whitespace, newline characters, or a byte order mark (BOM).
- **First file in the archive**: When packaging the EPUB into a ZIP container, `mimetype` must be the first file added to the archive.
- **Uncompressed and unencrypted**: The file must be stored uncompressed (using the ZIP `STORE` method rather than `DEFLATE`) and unencrypted so that its contents remain readable as plain text in the raw archive header.
- **No extra ZIP header fields**: The ZIP local file header for `mimetype` must not include extra fields, as extra header bytes would shift the `application/epub+zip` string away from byte offset 38.

> [!NOTE]
> If you package an EPUB from the command line using `zip`, add the `mimetype` file first with compression disabled (`-0`) and extra file attributes excluded (`-X`) before adding the remaining directories:
>
> ```bash
> zip -0 -X book.epub mimetype
> zip -r -X book.epub META-INF OEBPS
> ```

## Specifications

- [EPUB 3.3: The `mimetype` file](https://www.w3.org/TR/epub-33/#sec-zip-container-mime)

## See also

- [EPUB structure](/en-US/docs/Related/EPUB/Structure)
- [Open Container Format (OCF)](/en-US/docs/Related/EPUB/Structure/OCF)
- [`META-INF` directory](/en-US/docs/Related/EPUB/Structure/META-INF)
- [Common MIME types](/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types)
