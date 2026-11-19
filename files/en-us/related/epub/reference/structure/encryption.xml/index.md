---
title: EPUB encryption.xml file
short-title: encryption.xml
slug: Related/EPUB/Reference/Structure/encryption.xml
page-type: guide
sidebar: epub
---

The optional `encryption.xml` file in [`META-INF`](/en-US/docs/Related/EPUB/Reference/Structure/META-INF) lists any encrypted or obfuscated files in an EPUB archive and describes how to decode them. `META-INF/encryption.xml` is required whenever any resource in the container is encrypted or obfuscated; otherwise it is omitted.

In practice, `encryption.xml` is used for two purposes:

- [Font obfuscation](#font_obfuscation), which is the only reason an ebook author or authoring tool normally creates `encryption.xml` directly.
- [Resource encryption for Digital Rights Management (DRM)](#resource_encryption_and_drm), which is added after authoring by bookstores, library lending platforms, and DRM services—publishers normally deliver unencrypted EPUBs to distributors.

## Elements and attributes

`encryption.xml` combines a container `<encryption>` wrapper with elements from [W3C XML Encryption](https://www.w3.org/TR/xmlenc-core1/) (`http://www.w3.org/2001/04/xmlenc#`), [W3C XML Signature](https://www.w3.org/TR/xmldsig-core1/) (`http://www.w3.org/2000/09/xmldsig#`), and an EPUB compression namespace:

- `<encryption>`
  - : Required root element in the `urn:oasis:names:tc:opendocument:xmlns:container` namespace. Takes no attributes and contains one or more `<EncryptedData>` or `<EncryptedKey>` elements in any order.
- `<EncryptedData>` (`http://www.w3.org/2001/04/xmlenc#`)
  - : Describes one encrypted or obfuscated file in the archive. In EPUB, `<EncryptedData>` typically contains:
    - `<EncryptionMethod Algorithm="..." />`
      - : Identifies the encryption cipher (such as `http://www.w3.org/2001/04/xmlenc#aes256-cbc` or `http://www.w3.org/2001/04/xmlenc#kw-aes128`) or font obfuscation algorithm (`http://www.idpf.org/2008/embedding`).
    - `<KeyInfo>` (`http://www.w3.org/2000/09/xmldsig#`, optional)
      - : Tells the reading system how to locate the decryption key—for example, with a `<RetrievalMethod>` element pointing to an `<EncryptedKey>` in `encryption.xml` or to a DRM license file in the archive (such as `META-INF/license.lcpl` in Readium LCP). Omitted for font obfuscation.
    - `<CipherData>` and `<CipherReference URI="..." />`
      - : Gives the path to the encrypted or obfuscated file relative to the archive root (see [Relative paths across an EPUB](/en-US/docs/Related/EPUB/Reference/Structure/Content#relative_paths_across_an_epub)). Encrypted bytes replace the unencrypted file at its original path in the archive rather than being placed inline inside `encryption.xml`.
    - `<EncryptionProperties>` and `<EncryptionProperty>` (optional)
      - : Holds metadata about how the resource was prepared before encryption, most notably EPUB's `<Compression>` element:
        - `<Compression>` (`http://www.idpf.org/2016/encryption#compression`)
          - : Empty element recording pre-encryption compression so reading systems know whether to decompress after decrypting and know the unpacked byte size ahead of playback. Requires `Method` (`"8"` for Deflate or `"0"` for uncompressed) and `OriginalLength` (the original file size in bytes before compression and encryption).
- `<EncryptedKey>` (`http://www.w3.org/2001/04/xmlenc#`, optional)
  - : Stores a wrapped decryption key directly in `encryption.xml` (referenced by `Id` from `<EncryptedData>`) when a DRM system does not keep keys in a separate license file.

## Resource encryption and DRM

Password-protected ZIP encryption is not permitted in [OCF](/en-US/docs/Related/EPUB/Reference/Structure/OCF). Instead, OCF encrypts individual whole files independently, leaving the archive's directory tree, filenames, and [package file](/en-US/docs/Related/EPUB/Reference/Package) readable so reading systems can inspect metadata and decrypt chapters on demand.

When resources are encrypted in an EPUB:

- The following files must never be encrypted: [`mimetype`](/en-US/docs/Related/EPUB/Reference/Structure/mimetype), any reserved file in [`META-INF`](/en-US/docs/Related/EPUB/Reference/Structure/META-INF) ([`container.xml`](/en-US/docs/Related/EPUB/Reference/Structure/container.xml), `encryption.xml`, `manifest.xml`, `metadata.xml`, `rights.xml`, `signatures.xml`), and any [package file](/en-US/docs/Related/EPUB/Reference/Package).
- Text and other non-codec files (such as XHTML, CSS, or SVG) should be Deflate-compressed before encryption and record `<Compression Method="8" OriginalLength="..." />` inside `<EncryptionProperties>`. Already-compressed audio and video streams should not be compressed before encryption, because double compression adds overhead and prevents byte-range seeking during media playback.
- Encrypted files themselves should be stored uncompressed (`STORE`) in the ZIP archive, since encrypted ciphertext does not compress further.
- If files are digitally signed in `META-INF/signatures.xml` as well as encrypted, signed entries can use the [XML Signature Decryption Transform](https://www.w3.org/TR/xmlenc-decrypt/) so validators can tell whether a file was signed before or after encryption.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<encryption xmlns="urn:oasis:names:tc:opendocument:xmlns:container"
            xmlns:enc="http://www.w3.org/2001/04/xmlenc#"
            xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
  <enc:EncryptedKey Id="content-key">
    <enc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-oaep-mgf1p" />
    <enc:CipherData>
      <enc:CipherValue>d3JhcHBlZC1jb250ZW50LWFlcy1rZXktYnl0ZXM=</enc:CipherValue>
    </enc:CipherData>
  </enc:EncryptedKey>

  <enc:EncryptedData Id="ed-ch1">
    <enc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes256-cbc" />
    <ds:KeyInfo>
      <ds:RetrievalMethod URI="#content-key" Type="http://www.w3.org/2001/04/xmlenc#EncryptedKey" />
    </ds:KeyInfo>
    <enc:CipherData>
      <enc:CipherReference URI="EPUB/text/chapter-1.xhtml" />
    </enc:CipherData>
    <enc:EncryptionProperties>
      <enc:EncryptionProperty xmlns:cp="http://www.idpf.org/2016/encryption#compression">
        <cp:Compression Method="8" OriginalLength="24580" />
      </enc:EncryptionProperty>
    </enc:EncryptionProperties>
  </enc:EncryptedData>
</encryption>
```

EPUB 3.3 standardizes only this `encryption.xml` container syntax, not a DRM key-exchange or licensing protocol, and reading systems are not required to decrypt encrypted resources. Retailers and library platforms apply their own DRM profiles during distribution (such as Readium LCP, Adobe ADEPT, or Apple FairPlay), so a DRM-encrypted EPUB opens only in reading apps that support that ecosystem's DRM.

## Font obfuscation

Some commercial font foundries permit embedding a font in an EPUB only if the font file is tied to the book so it cannot be unzipped and installed directly on another computer. Although obfuscation is not cryptographic encryption, EPUB uses `META-INF/encryption.xml` to tell reading systems which [embedded fonts](/en-US/docs/Related/EPUB/Reference/Content/Fonts) are obfuscated.

Use font obfuscation only when no simpler option—such as open-licensed fonts, WOFF2 compression, or glyph subsetting—satisfies your font license:

1. Strip all XML whitespace characters (`U+0020`, `U+0009`, `U+000D`, and `U+000A`) from the book's primary [`<dc:identifier>`](/en-US/docs/Related/EPUB/Reference/Package/Metadata) (referenced by `unique-identifier` on `<package>`) and compute the 20-byte SHA-1 digest of its UTF-8 bytes.
2. Before compressing the font into the ZIP archive, XOR the first 1,040 bytes of the raw font file (or the entire file if shorter than 1,040 bytes) against the repeating 20-byte SHA-1 key, leaving the rest of the file unchanged. (Because obfuscation only XORs the file header rather than encrypting the full stream, obfuscated fonts can still be Deflate-compressed in the ZIP archive.)
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

Declare every obfuscated font in the [manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest) with its normal font core media type (`font/woff2`, `font/woff`, `font/otf`, or `font/ttf`, or a legacy alias). Only font core media types may use `http://www.idpf.org/2008/embedding`, and you must not include the obfuscation key in `encryption.xml`. (When inspecting older EPUBs exported from tools such as Adobe InDesign, you may also encounter the legacy Adobe font-mangling algorithm `http://ns.adobe.com/pdf/enc#RC`; use the standard `http://www.idpf.org/2008/embedding` algorithm in EPUB 3.)

Font obfuscation has two important limits:

- It only deters casual extraction from an unzipped EPUB; because the key comes from the public `<dc:identifier>` in the package file, it is not cryptographic protection.
- Reading systems are not required to support font deobfuscation. Non-supporting readers ignore obfuscated fonts and fall back to the next typeface in your CSS `font-family` stack.

## Specifications

- [EPUB 3.3: Encryption file (`encryption.xml`)](https://www.w3.org/TR/epub-33/#sec-container-metainf-encryption.xml)
- [EPUB 3.3: Font obfuscation](https://www.w3.org/TR/epub-33/#sec-font-obfuscation)
- [EPUB Reading Systems 3.3: Font obfuscation](https://www.w3.org/TR/epub-rs-33/#sec-container-fobfus)
- [XML Encryption Syntax and Processing Version 1.1](https://www.w3.org/TR/xmlenc-core1/)
