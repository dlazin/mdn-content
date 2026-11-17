---
title: EPUB META-INF directory
short-title: META-INF
slug: Related/EPUB/Structure/META-INF
page-type: guide
sidebar: epub
---

Every EPUB contains a directory named **`META-INF`** in the root directory of the container. This directory holds container-level files that tell reading systems how to locate and process the publication, most importantly the required `container.xml` file.

The `META-INF` directory only stores information about the container itself. Publication content—such as XHTML documents, CSS stylesheets, images, audio, and the [package file](/en-US/docs/Related/EPUB/Package) that holds the book's bibliographic metadata—belongs in the [content directory](/en-US/docs/Related/EPUB/Structure/Content), not in `META-INF`.

## The `container.xml` file

Every `META-INF` directory must include a file named `container.xml`. This file tells the reading system where to find the publication's [package file](/en-US/docs/Related/EPUB/Package) (`.opf`), which serves as the manifest and structural index for the ebook.

### Example `container.xml`

In almost all EPUB publications, `META-INF/container.xml` has the following structure, varying only in the `full-path` attribute:

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
  - : The root element of `container.xml`. It must declare the XML namespace `xmlns="urn:oasis:names:tc:opendocument:xmlns:container"` and the attribute `version="1.0"`.
- `<rootfiles>`
  - : A required wrapper element that contains one or more `<rootfile>` elements.
- `<rootfile>`
  - : Identifies a [package file](/en-US/docs/Related/EPUB/Package) for the publication. Although the specification allows multiple `<rootfile>` elements, an EPUB almost always contains only one, and reading systems are only required to process the first `<rootfile>` listed. Each `<rootfile>` element requires two attributes:
    - `full-path`: The path to the package file, relative to the root directory of the unzipped EPUB container (not relative to the `META-INF` directory). This value changes from book to book depending on how the [content directory](/en-US/docs/Related/EPUB/Structure/Content) and package file are named—for example, `OEBPS/package.opf`, `OPS/content.opf`, or `EPUB/package.opf`.
    - `media-type`: The MIME media type of the package file. For an EPUB package file, this value is always `application/oebps-package+xml`.

## Optional files in `META-INF`

In addition to `container.xml`, the EPUB specification reserves several optional filenames in the `META-INF` directory. Most EPUBs do not need any of these optional files unless they use font obfuscation or Digital Rights Management (DRM):

- `encryption.xml`
  - : Describes how files in the container are encrypted and holds the keys needed to decrypt them. In practice, this file is almost always used either for [font obfuscation](/en-US/docs/Related/EPUB/Content/Fonts#font_licensing_and_obfuscation) (protecting licensed font files embedded in the ebook) or for DRM schemes.
- `signatures.xml`
  - : Contains digital signatures for files in the container, allowing a reading system to verify the authenticity and integrity of the publication.
- `metadata.xml`
  - : Reserved for container-level metadata. The EPUB specification does not define a required format for this file; standard ebook metadata (such as title, creator, and identifier) is placed in the [package file](/en-US/docs/Related/EPUB/Package) instead.
- `rights.xml`
  - : Reserved for Digital Rights Management (DRM) information. The EPUB specification does not standardize a format for this file, so its contents depend on the specific DRM system used by a retailer or distributor.
- `manifest.xml`
  - : A container manifest format inherited from the OASIS OpenDocument Format (ODF) standard. It is not used by EPUB reading systems, which read the publication manifest from the [package file](/en-US/docs/Related/EPUB/Package) instead.

## Specifications

- [EPUB 3.3: `META-INF` directory](https://www.w3.org/TR/epub-33/#sec-container-metainf)

## See also

- [EPUB structure](/en-US/docs/Related/EPUB/Structure)
- [The EPUB `mimetype` file](/en-US/docs/Related/EPUB/Structure/mimetype)
- [Content directory](/en-US/docs/Related/EPUB/Structure/Content)
- [EPUB package file](/en-US/docs/Related/EPUB/Package)
