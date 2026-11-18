---
title: EPUB META-INF directory
short-title: META-INF
slug: Related/EPUB/Structure/META-INF
page-type: guide
sidebar: epub
---

Every EPUB archive contains a `META-INF` directory at its root. This directory stores container configuration files—most importantly the required `container.xml` file that tells reading systems where to find the book's [package file](/en-US/docs/Related/EPUB/Package).

`META-INF` only holds container-level files. Chapters, stylesheets, images, metadata records, and the `.opf` package file belong outside `META-INF` in the [content directory](/en-US/docs/Related/EPUB/Structure/Content).

## The `container.xml` file

Every `META-INF` directory must contain a `container.xml` file pointing to the `.opf` [package file](/en-US/docs/Related/EPUB/Package). In almost all EPUBs, `META-INF/container.xml` has the following structure, changing only the `full-path` value:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<container xmlns="urn:oasis:names:tc:opendocument:xmlns:container" version="1.0">
  <rootfiles>
    <rootfile full-path="OEBPS/package.opf"
      media-type="application/oebps-package+xml" />
  </rootfiles>
</container>
```

### Elements and attributes

- `<container>`
  - : Required root element. Must declare `xmlns="urn:oasis:names:tc:opendocument:xmlns:container"` and `version="1.0"`.
- `<rootfiles>`
  - : Required child of `<container>` that wraps one or more `<rootfile>` elements.
- `<rootfile>`
  - : Identifies a [package file](/en-US/docs/Related/EPUB/Package) in the archive. Although multiple `<rootfile>` elements are allowed for multi-rendition books, almost all EPUBs have just one, and reading systems are only required to use the first `<rootfile>`. Requires two attributes:
    - `full-path`
      - : Path to the package file relative to the root of the EPUB archive (not relative to `META-INF/`), such as `OEBPS/package.opf`, `OPS/content.opf`, or `EPUB/package.opf`.
    - `media-type`
      - : Must be `application/oebps-package+xml`.

## Optional files in `META-INF`

EPUB reserves several optional filenames in `META-INF`. Most ebooks do not include any of these unless they use font obfuscation, digital signatures, or Digital Rights Management (DRM):

- `encryption.xml`
  - : Lists encrypted or obfuscated files in the archive and how to decode them. Most commonly used for [font obfuscation](/en-US/docs/Related/EPUB/Content/Fonts#font_licensing_and_obfuscation) or vendor DRM.
- `signatures.xml`
  - : Stores XML digital signatures used to verify that files in the archive have not been modified.
- `metadata.xml`
  - : Reserved for container-level metadata. EPUB defines no standard format for this file; place book metadata in the [package file](/en-US/docs/Related/EPUB/Package/Metadata) instead.
- `rights.xml`
  - : Reserved for vendor-specific DRM information. EPUB does not standardize its format.
- `manifest.xml`
  - : Inherited from the OpenDocument Format (ODF) container specification. EPUB reading systems ignore it and read the manifest from the [package file](/en-US/docs/Related/EPUB/Package/Manifest) instead.

## Specifications

- [EPUB 3.3: `META-INF` directory](https://www.w3.org/TR/epub-33/#sec-container-metainf)

## See also

- [EPUB structure](/en-US/docs/Related/EPUB/Structure)
- [EPUB `mimetype` file](/en-US/docs/Related/EPUB/Structure/mimetype)
- [EPUB content directory](/en-US/docs/Related/EPUB/Structure/Content)
- [EPUB package file](/en-US/docs/Related/EPUB/Package)
