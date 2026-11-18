---
title: EPUB encryption.xml file
short-title: encryption.xml
slug: Related/EPUB/Reference/Structure/encryption.xml
page-type: guide
sidebar: epub
---

The optional `encryption.xml` file in [`META-INF`](/en-US/docs/Related/EPUB/Reference/Structure/META-INF) lists any encrypted or obfuscated files in an EPUB archive and describes how to decode them. Most EPUBs omit `encryption.xml` unless they use [font obfuscation](#font_obfuscation) or vendor Digital Rights Management (DRM).

`META-INF/encryption.xml` is required if any resource in the container is encrypted or obfuscated.

## Structure and encryption rules

`encryption.xml` uses a root `<encryption>` element in the `urn:oasis:names:tc:opendocument:xmlns:container` namespace containing `<EncryptedData>` and optional `<EncryptedKey>` elements from [W3C XML Encryption](https://www.w3.org/TR/xmlenc-core1/) (`http://www.w3.org/2001/04/xmlenc#`). Each `<EncryptedData>` element identifies an encrypted or obfuscated file using `<CipherReference URI="...">` with a path relative to the archive root.

Rather than password-protecting the ZIP archive as a whole, [OCF](/en-US/docs/Related/EPUB/Reference/Structure/OCF) encrypts individual files in their entirety so reading systems can decrypt resources on demand:

- The following files must never be encrypted: [`mimetype`](/en-US/docs/Related/EPUB/Reference/Structure/mimetype), any reserved file in `META-INF` ([`container.xml`](/en-US/docs/Related/EPUB/Reference/Structure/container.xml), `encryption.xml`, `manifest.xml`, `metadata.xml`, `rights.xml`, `signatures.xml`), and the [package file](/en-US/docs/Related/EPUB/Reference/Package).
- Text and other non-codec files (such as XHTML, CSS, or SVG) should be Deflate-compressed before encryption, whereas already-compressed audio and video streams should not be compressed before encryption. Encrypted files should then be stored uncompressed (`STORE`) in the ZIP archive.
- EPUB does not standardize a DRM key-management system. Retailers and library platforms that apply DRM (such as Readium LCP or Adobe ADEPT) generate their own `<EncryptedKey>` and `<EncryptedData>` entries in `encryption.xml` during distribution.

## Font obfuscation

Some commercial font foundries permit embedding a font in an EPUB only if the font file is tied to the book so it cannot be unzipped and installed directly on another computer. Although obfuscation is not cryptographic encryption, EPUB uses `META-INF/encryption.xml` to tell reading systems which [embedded fonts](/en-US/docs/Related/EPUB/Reference/Content/Fonts) are obfuscated.

Use font obfuscation only when no simpler option—such as open-licensed fonts, WOFF2 compression, or glyph subsetting—satisfies your font license:

1. Strip all XML whitespace characters (`U+0020`, `U+0009`, `U+000D`, and `U+000A`) from the book's primary [`<dc:identifier>`](/en-US/docs/Related/EPUB/Reference/Package/Metadata) (referenced by `unique-identifier` on `<package>`) and compute the 20-byte SHA-1 digest of its UTF-8 bytes.
2. Before compressing the font into the ZIP archive, XOR the first 1,040 bytes of the raw font file (or the entire file if shorter than 1,040 bytes) against the repeating 20-byte SHA-1 key, leaving the rest of the file unchanged.
3. List each obfuscated font in `META-INF/encryption.xml` with `Algorithm="http://www.idpf.org/2008/embedding"` and a `URI` relative to the container root:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <encryption xmlns="urn:oasis:names:tc:opendocument:xmlns:container"
               xmlns:enc="http://www.w3.org/2001/04/xmlenc#">
     <enc:EncryptedData>
       <enc:EncryptionMethod Algorithm="http://www.idpf.org/2008/embedding" />
       <enc:CipherData>
         <enc:CipherReference URI="EPUB/fonts/display-heading.otf" />
       </enc:CipherData>
     </enc:EncryptedData>
   </encryption>
   ```

Declare every obfuscated font in the [manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest) with its normal font core media type (`font/woff2`, `font/woff`, `font/otf`, or `font/ttf`, or a legacy alias). Only font core media types may use this obfuscation algorithm, and you must not include the obfuscation key in `encryption.xml`.

Font obfuscation has two important limits:

- It only deters casual extraction from an unzipped EPUB; because the key comes from the public `<dc:identifier>` in the package file, it is not cryptographic protection.
- Reading systems are not required to support font deobfuscation. Non-supporting readers ignore obfuscated fonts and fall back to the next typeface in your CSS `font-family` stack.

## Specifications

- [EPUB 3.3: Encryption file (`encryption.xml`)](https://www.w3.org/TR/epub-33/#sec-container-metainf-encryption.xml)
- [EPUB 3.3: Font obfuscation](https://www.w3.org/TR/epub-33/#sec-font-obfuscation)
- [EPUB Reading Systems 3.3: Font obfuscation](https://www.w3.org/TR/epub-rs-33/#sec-container-fobfus)
