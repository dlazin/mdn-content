---
title: EPUB structure
short-title: Structure
slug: Related/EPUB/Structure
page-type: guide
sidebar: epub
---

An EPUB is a ZIP archive that follows the [Open Container Format (OCF)](/en-US/docs/Related/EPUB/Structure/OCF) specification. When unzipped, an EPUB has a [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype) file in its root directory alongside a [`META-INF`](/en-US/docs/Related/EPUB/Structure/META-INF) directory and, typically, a [content directory](/en-US/docs/Related/EPUB/Structure/Content) (commonly named `EPUB`, `OEBPS`, or `OPS`).

```plain
book.epub/
├── mimetype
├── META-INF/
│   └── container.xml
└── OEBPS/
    ├── package.opf
    ├── nav.xhtml
    ├── images/
    │   └── cover.jpg
    ├── styles/
    │   └── main.css
    └── text/
        ├── cover.xhtml
        └── chapter-1.xhtml
```

## Loading an EPUB

When a user opens an EPUB, the reading system loads its files in four steps:

1. It checks the uncompressed [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype) file at the start of the ZIP archive for the string `application/epub+zip`, confirming the file is an EPUB.
2. It reads [`META-INF/container.xml`](/en-US/docs/Related/EPUB/Structure/META-INF), whose `<rootfile>` element gives the path to the [package file](/en-US/docs/Related/EPUB/Package) (for example, `OEBPS/package.opf`).
3. It parses the package file to read the [metadata](/en-US/docs/Related/EPUB/Package/Metadata) (title, author, language, identifier), [manifest](/en-US/docs/Related/EPUB/Package/Manifest) (every file in the book, including the navigation document), and [spine](/en-US/docs/Related/EPUB/Package/Spine) (the chapter order).
4. It uses the [navigation document](/en-US/docs/Related/EPUB/Navigation) to populate the table of contents menu and renders the [publication content](/en-US/docs/Related/EPUB/Content) in spine order.

## Structural components

- [Open Container Format](/en-US/docs/Related/EPUB/Structure/OCF)
  - : The ZIP archive rules, filename restrictions, compression requirements, and URL boundaries.
- [`mimetype` file](/en-US/docs/Related/EPUB/Structure/mimetype)
  - : The uncompressed first file in the root of every EPUB archive (`application/epub+zip`).
- [`META-INF` directory](/en-US/docs/Related/EPUB/Structure/META-INF)
  - : The folder that contains `container.xml` and optional files such as `encryption.xml` and `signatures.xml`.
- [Content directory](/en-US/docs/Related/EPUB/Structure/Content)
  - : The folder that contains the [package file](/en-US/docs/Related/EPUB/Package), [navigation document](/en-US/docs/Related/EPUB/Navigation), and [publication content](/en-US/docs/Related/EPUB/Content).

## Specifications

- [EPUB 3.3: Open Container Format (OCF)](https://www.w3.org/TR/epub-33/#sec-ocf)
- [EPUB 3.3: File and directory structure](https://www.w3.org/TR/epub-33/#sec-container-file-and-dir-structure)
