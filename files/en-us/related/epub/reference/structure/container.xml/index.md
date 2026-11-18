---
title: EPUB container.xml file
short-title: container.xml
slug: Related/EPUB/Reference/Structure/container.xml
page-type: guide
sidebar: epub
---

Every EPUB archive must include a `container.xml` file inside its root [`META-INF`](/en-US/docs/Related/EPUB/Reference/Structure/OCF#meta-inf_directory) directory (`META-INF/container.xml`). It tells reading systems where to find the book's [package file](/en-US/docs/Related/EPUB/Reference/Package).

Like other files in `META-INF`, `container.xml` must not be encrypted and must not be listed in the package [manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest).

## Structure

In almost all EPUBs, `META-INF/container.xml` has the following [XML](/en-US/docs/Web/XML/Guides/XML_introduction) structure, changing only the value of `full-path`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<container xmlns="urn:oasis:names:tc:opendocument:xmlns:container" version="1.0">
  <rootfiles>
    <rootfile full-path="OEBPS/package.opf" media-type="application/oebps-package+xml" />
  </rootfiles>
</container>
```

## Elements and attributes

All standard elements in `container.xml` belong to the `urn:oasis:names:tc:opendocument:xmlns:container` namespace:

- `<container>`
  - : Required root element. Must declare `xmlns="urn:oasis:names:tc:opendocument:xmlns:container"` and `version="1.0"`. Contains `<rootfiles>` (followed by an optional `<links>` element, which EPUB 3.3 defines no uses for outside multi-rendition specifications).
- `<rootfiles>`
  - : Required first child of `<container>` that wraps one or more `<rootfile>` elements.
- `<rootfile>`
  - : Empty element identifying a [package file](/en-US/docs/Related/EPUB/Reference/Package) in the archive. Although multiple `<rootfile>` elements are allowed for multi-rendition books (where each package file conforms to the same version of EPUB), almost all EPUBs have just one, and reading systems are only required to use the first `<rootfile>` as the default rendition. Requires two attributes:
    - `full-path`
      - : Path to the package file relative to the root of the EPUB archive (not relative to `META-INF`), such as `OEBPS/package.opf`, `OPS/content.opf`, or `EPUB/package.opf`. See [Relative paths across an EPUB](/en-US/docs/Related/EPUB/Reference/Structure/Content#relative_paths_across_an_epub).
    - `media-type`
      - : Must be `application/oebps-package+xml`.

## Specifications

- [EPUB 3.3: Container file (`container.xml`)](https://www.w3.org/TR/epub-33/#sec-container-metainf-container.xml)
- [EPUB Reading Systems 3.3: Container file (`container.xml`)](https://www.w3.org/TR/epub-rs-33/#sec-container-metainf-container.xml)
