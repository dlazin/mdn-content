---
title: EPUB structure
short-title: Structure
slug: Related/EPUB/Reference/Structure
page-type: guide
sidebar: epub
---

An EPUB is a ZIP archive that follows the [Open Container Format (OCF)](/en-US/docs/Related/EPUB/Reference/Structure/OCF) specification. When unzipped, an EPUB has a [`mimetype`](/en-US/docs/Related/EPUB/Reference/Structure/mimetype) file in its root directory alongside a [`META-INF`](/en-US/docs/Related/EPUB/Reference/Structure/META-INF) directory and, typically, a [content directory](/en-US/docs/Related/EPUB/Reference/Structure/Content) (commonly named `EPUB`, `OEBPS`, or `OPS`).

```plain
book.epub/
├── mimetype
├── META-INF/
│   ├── container.xml
│   └── encryption.xml
└── EPUB/
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

1. It checks the uncompressed [`mimetype`](/en-US/docs/Related/EPUB/Reference/Structure/mimetype) file at the start of the ZIP archive for the string `application/epub+zip`, confirming the file is an EPUB.
2. It reads [`META-INF/container.xml`](/en-US/docs/Related/EPUB/Reference/Structure/container.xml), whose `<rootfile>` element gives the path to the [package file](/en-US/docs/Related/EPUB/Reference/Package) (for example, `EPUB/package.opf`).
3. It parses the package file to read the [metadata](/en-US/docs/Related/EPUB/Reference/Package/Metadata) (title, author, language, identifier), [manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest) (every file in the book, including the navigation document), and [spine](/en-US/docs/Related/EPUB/Reference/Package/Spine) (the chapter order).
4. It uses the [navigation document](/en-US/docs/Related/EPUB/Reference/Navigation) to populate the table of contents menu and renders the [publication content](/en-US/docs/Related/EPUB/Reference/Content) in spine order.

## Structural components

- [Open Container Format](/en-US/docs/Related/EPUB/Reference/Structure/OCF)
  - : The ZIP archive rules, filename restrictions, compression requirements, and URL boundaries.
- [`mimetype` file](/en-US/docs/Related/EPUB/Reference/Structure/mimetype)
  - : The uncompressed first file in the root of every EPUB archive (`application/epub+zip`).
- [`META-INF` directory](/en-US/docs/Related/EPUB/Reference/Structure/META-INF)
  - : The root configuration folder that holds `container.xml` and optional files such as `encryption.xml` and `signatures.xml`.
- [`container.xml` file](/en-US/docs/Related/EPUB/Reference/Structure/container.xml)
  - : The required `META-INF/container.xml` file that points reading systems to the [package file](/en-US/docs/Related/EPUB/Reference/Package).
- [`encryption.xml` file](/en-US/docs/Related/EPUB/Reference/Structure/encryption.xml)
  - : The optional `META-INF/encryption.xml` file that declares encrypted resources and obfuscated fonts.
- [Content directory](/en-US/docs/Related/EPUB/Reference/Structure/Content)
  - : The folder that contains the [package file](/en-US/docs/Related/EPUB/Reference/Package), [navigation document](/en-US/docs/Related/EPUB/Reference/Navigation), and [publication content](/en-US/docs/Related/EPUB/Reference/Content).

## Specifications

- [EPUB 3.3: Open Container Format (OCF)](https://www.w3.org/TR/epub-33/#sec-ocf)
- [EPUB 3.3: File and directory structure](https://www.w3.org/TR/epub-33/#sec-container-file-and-dir-structure)
