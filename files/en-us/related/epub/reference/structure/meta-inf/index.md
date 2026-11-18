---
title: EPUB META-INF directory
short-title: META-INF
slug: Related/EPUB/Reference/Structure/META-INF
page-type: guide
sidebar: epub
---

Every EPUB archive contains a `META-INF` directory at its root that holds container-level [XML](/en-US/docs/Web/XML/Guides/XML_introduction) configuration files—most importantly [`container.xml`](/en-US/docs/Related/EPUB/Reference/Structure/container.xml), which tells reading systems where to find the [package file](/en-US/docs/Related/EPUB/Reference/Package).

Book content and the package file belong outside `META-INF` in the [content directory](/en-US/docs/Related/EPUB/Reference/Structure/Content). Relative URLs inside `META-INF` files resolve from the archive root rather than from `META-INF` (see [Relative paths across an EPUB](/en-US/docs/Related/EPUB/Reference/Structure/Content#relative_paths_across_an_epub)), and files in `META-INF` must not be listed in the package [manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest).

## Reserved files in `META-INF`

EPUB reserves six filenames in `META-INF`. Only `container.xml` is required; most ebooks omit the optional files unless they use font obfuscation, digital signatures, or Digital Rights Management (DRM):

- [`container.xml`](/en-US/docs/Related/EPUB/Reference/Structure/container.xml) (required)
  - : Identifies the path to the [package file](/en-US/docs/Related/EPUB/Reference/Package) in the archive.
- [`encryption.xml`](/en-US/docs/Related/EPUB/Reference/Structure/encryption.xml) (optional)
  - : Lists encrypted or obfuscated files in the archive and how to decode them, most commonly for [font obfuscation](/en-US/docs/Related/EPUB/Reference/Structure/encryption.xml#font_obfuscation) or vendor DRM.
- `manifest.xml` (optional)
  - : Inherited from the OpenDocument Format (ODF) container specification. EPUB reading systems ignore it and read the manifest from the [package file](/en-US/docs/Related/EPUB/Reference/Package/Manifest) instead.
- `metadata.xml` (optional)
  - : Reserved for container-level metadata. EPUB defines no standard format for this file; place book metadata in the [package file](/en-US/docs/Related/EPUB/Reference/Package/Metadata) instead.
- `rights.xml` (optional)
  - : Reserved for vendor-specific DRM information. EPUB does not standardize its format.
- `signatures.xml` (optional)
  - : Stores W3C XML Digital Signatures used to verify that files in the archive have not been modified, though reading systems are not required to check signatures.

## Specifications

- [EPUB 3.3: `META-INF` directory](https://www.w3.org/TR/epub-33/#sec-container-metainf)
- [EPUB Reading Systems 3.3: `META-INF` directory](https://www.w3.org/TR/epub-rs-33/#sec-container-metainf)
