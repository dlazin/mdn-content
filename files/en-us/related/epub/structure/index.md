---
title: EPUB structure
short-title: Overview
slug: Related/EPUB/Structure
page-type: guide
sidebar: epub
---

An `.epub` file is a single ZIP archive formatted according to the [Open Container Format (OCF)](/en-US/docs/Related/EPUB/Structure/OCF). When unzipped, a typical EPUB contains a root [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype) file and two top-level directories: a required [`META-INF`](/en-US/docs/Related/EPUB/Structure/META-INF) directory and a [content directory](/en-US/docs/Related/EPUB/Structure/Content) (commonly named `OEBPS`, `OPS`, or `EPUB`):

```plain
book.epub/
├── mimetype
├── META-INF/
│   └── container.xml
└── OEBPS/
    ├── package.opf
    ├── nav.xhtml
    ├── styles/
    │   └── main.css
    ├── images/
    │   └── cover.jpg
    └── text/
        ├── cover.xhtml
        └── chapter-1.xhtml
```

## How reading systems open an EPUB

When you open an `.epub` file in an e-reader app or device, the reading system locates and loads the book's files in a fixed sequence:

1. **Identify the archive ([`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype))**: The first entry in the ZIP container is an uncompressed `mimetype` file containing the string `application/epub+zip`, confirming that the archive is an EPUB publication.
2. **Locate the package file ([`META-INF/container.xml`](/en-US/docs/Related/EPUB/Structure/META-INF))**: The reading system reads `META-INF/container.xml`, whose `<rootfile>` element specifies the container-relative path to the publication's [package file](/en-US/docs/Related/EPUB/Package) (such as `OEBPS/package.opf`).
3. **Read metadata, inventory, and reading order ([package file](/en-US/docs/Related/EPUB/Package))**: Inside the `.opf` package file, the reading system reads [`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata) (title, author, language, identifier), [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) (every file in the book, including which file has `properties="nav"`), and [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine) (the sequence of chapters).
4. **Build navigation and render content**: Using the [navigation document](/en-US/docs/Related/EPUB/Navigation) (`nav.xhtml`) and the `<spine>`, the reading system populates its table of contents menu and renders the [publication content](/en-US/docs/Related/EPUB/Content) documents.

## Structural components

- [Open Container Format (OCF)](/en-US/docs/Related/EPUB/Structure/OCF)
  - : The ZIP archive rules, file and path naming restrictions, compression requirements, and container URL boundaries that govern `.epub` files.
- [`mimetype` file](/en-US/docs/Related/EPUB/Structure/mimetype)
  - : The required uncompressed first file in the root of every EPUB archive (`application/epub+zip`).
- [`META-INF` directory](/en-US/docs/Related/EPUB/Structure/META-INF)
  - : The reserved root configuration directory containing `container.xml` and optional files such as `encryption.xml` and `signatures.xml`.
- [Content directory](/en-US/docs/Related/EPUB/Structure/Content)
  - : The directory (conventionally named `OEBPS`, `OPS`, or `EPUB`) where the [package file](/en-US/docs/Related/EPUB/Package), [navigation document](/en-US/docs/Related/EPUB/Navigation), and all [publication content](/en-US/docs/Related/EPUB/Content) resources are organized.

## Specifications

- [EPUB 3.3: Open Container Format (OCF)](https://www.w3.org/TR/epub-33/#sec-ocf)
- [EPUB 3.3: File and directory structure](https://www.w3.org/TR/epub-33/#sec-container-file-and-dir-structure)

## See also

- [Introduction to EPUB](/en-US/docs/Related/EPUB/Overview)
- [EPUB package file](/en-US/docs/Related/EPUB/Package)
- [EPUB navigation document](/en-US/docs/Related/EPUB/Navigation)
- [EPUB publication content](/en-US/docs/Related/EPUB/Content)
