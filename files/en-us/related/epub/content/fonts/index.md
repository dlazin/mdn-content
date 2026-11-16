---
title: EPUB fonts
short-title: Fonts
slug: Related/EPUB/Content/Fonts
page-type: guide
sidebar: epub
---

By default, EPUB reading systems render text using their own built-in fonts and let readers switch typefaces according to personal preference or visual needs. When a publication needs custom typography—such as display fonts for chapter headings, specialized mathematical or phonetic glyphs, or support for scripts that may not be preinstalled on all devices—you can bundle font files in the [content directory](/en-US/docs/Related/EPUB/Structure/Content), declare them in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest), and load them in CSS with [`@font-face`](/en-US/docs/Web/CSS/Reference/At-rules/@font-face).

## Supported font formats and media types

EPUB 3 defines four **font core media types** that visual reading systems are expected to support without fallbacks:

| Format | Preferred `media-type` | Legacy `media-type` aliases | CSS `format()` value |
| --- | --- | --- | --- |
| WOFF2 (`.woff2`) | `font/woff2` | None | `"woff2"` |
| WOFF (`.woff`) | `font/woff` | `application/font-woff` | `"woff"` |
| OpenType (`.otf`) | `font/otf` | `application/font-sfnt`, `application/vnd.ms-opentype` | `"opentype"` |
| TrueType (`.ttf`) | `font/ttf` | `application/font-sfnt` | `"truetype"` |

When declaring fonts in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest), always use the preferred `font/*` media type (`font/woff2`, `font/woff`, `font/otf`, or `font/ttf`) in new EPUB 3 publications. The `application/*` strings are legacy aliases retained for backward compatibility with older authoring tools.

- **Choosing a format**: WOFF2 (`font/woff2`) produces the smallest `.epub` file sizes thanks to built-in compression, while OpenType (`font/otf`) and TrueType (`font/ttf`) offer broad compatibility across both modern and older e-readers.
- **Exempt font formats**: Any font format outside the four core media types is classified as an *exempt resource* in EPUB 3. Unlike other non-core media types, exempt fonts do not require a manifest `fallback` attribute, because CSS `font-family` stacks automatically fall back to a system font when a reading system cannot load a font file.

## Embedding and styling fonts

To embed custom fonts in an EPUB:

1. Copy the font files into your [content directory](/en-US/docs/Related/EPUB/Structure/Content) (for example, `OEBPS/fonts/`).
2. Declare every font file as an `<item>` in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest):

   ```xml
   <manifest>
     <item id="font-regular" href="fonts/literata-regular.woff2" media-type="font/woff2" />
     <item id="font-italic" href="fonts/literata-italic.woff2" media-type="font/woff2" />
     <item id="font-bold" href="fonts/literata-bold.woff2" media-type="font/woff2" />
     <item id="css" href="styles/main.css" media-type="text/css" />
     <!-- other manifest items -->
   </manifest>
   ```

3. Define [`@font-face`](/en-US/docs/Web/CSS/Reference/At-rules/@font-face) rules in your CSS stylesheet—using paths relative to the stylesheet itself—and include fallback font families in every `font-family` declaration:

   ```css
   @font-face {
     font-family: "Literata";
     src: url("../fonts/literata-regular.woff2") format("woff2");
     font-weight: 400;
     font-style: normal;
   }

   @font-face {
     font-family: "Literata";
     src: url("../fonts/literata-italic.woff2") format("woff2");
     font-weight: 400;
     font-style: italic;
   }

   @font-face {
     font-family: "Literata";
     src: url("../fonts/literata-bold.woff2") format("woff2");
     font-weight: 700;
     font-style: normal;
   }

   h1,
   h2 {
     font-family: "Literata", "Georgia", serif;
   }
   ```

> [!NOTE]
> Many reading systems allow users to override publisher body-text fonts (for example, to switch to a high-legibility or dyslexic-friendly typeface) or ignore embedded body fonts unless the reader explicitly selects "Publisher Default." Avoid forcing custom fonts on body prose with `!important`, and always test that your book remains readable when the user switches to a built-in device font.

### Remotely hosted fonts

Fonts are one of the few publication resource types that EPUB 3 permits hosting outside the `.epub` container over `https:`. If a CSS stylesheet (or [XHTML](/en-US/docs/Glossary/XHTML) document) references a remote font URL:

- You must still declare the remote font URL as an `<item>` in `<manifest>`.
- You must add `properties="remote-resources"` to the `<manifest>` item for the stylesheet or content document that references the remote font.

Because ebooks are frequently read offline on e-ink devices or in airplane mode, bundling font files inside the EPUB container is strongly preferred over loading remote web fonts.

## Font licensing and obfuscation

Because an `.epub` file is a standard ZIP container, anyone can unzip an ebook and inspect unencrypted files inside it. Before embedding a commercial font, verify that your font license explicitly covers ebook or EPUB distribution (standard desktop and webfont licenses often do not).

Some commercial font foundries allow EPUB embedding only if the font files are bound to the publication so they cannot be casually extracted and installed on a computer. To satisfy these license terms without applying full Digital Rights Management (DRM), EPUB defines a lightweight **font obfuscation** mechanism:

1. **Key derivation**: Strip all XML whitespace characters (`U+0020`, `U+0009`, `U+000D`, and `U+000A`) from the publication's primary [`<dc:identifier>`](/en-US/docs/Related/EPUB/Package/Metadata) (the one referenced by `unique-identifier` on `<package>`) and compute the 20-byte SHA-1 digest of its UTF-8 bytes.
2. **Byte scrambling**: Before compressing the font into the ZIP archive, XOR the first 1,040 bytes of the raw font file against the repeating 20-byte SHA-1 key, leaving the remainder of the file unchanged.
3. **Declaring obfuscated fonts in `META-INF/encryption.xml`**: List each obfuscated font in [`META-INF/encryption.xml`](/en-US/docs/Related/EPUB/Structure/META-INF) using an `<enc:EncryptedData>` block with `Algorithm="http://www.idpf.org/2008/embedding"` and a `URI` relative to the root of the EPUB container:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <encryption xmlns="urn:oasis:names:tc:opendocument:xmlns:container"
               xmlns:enc="http://www.w3.org/2001/04/xmlenc#">
     <enc:EncryptedData>
       <enc:EncryptionMethod Algorithm="http://www.idpf.org/2008/embedding" />
       <enc:CipherData>
         <enc:CipherReference URI="OEBPS/fonts/display-heading.otf" />
       </enc:CipherData>
     </enc:EncryptedData>
   </encryption>
   ```

   Every obfuscated font listed in `META-INF/encryption.xml` must be declared in `<manifest>` with one of the font core media types (`font/woff2`, `font/woff`, `font/otf`, or `font/ttf`, or their core aliases)—not an arbitrary non-core MIME type. Do not store the obfuscation key inside `encryption.xml`.

Keep two limitations of font obfuscation in mind:

- **Not cryptographic security**: Font obfuscation only deters casual copying from an unzipped `.epub` folder; because the key is derived from the public `<dc:identifier>` in the package file, a determined user or reading system can reverse it.
- **Optional reading system support**: EPUB reading systems are not required to support font deobfuscation. If a reading system does not deobfuscate fonts, it falls back to the next typeface in your CSS `font-family` list. Using open-licensed fonts (such as SIL Open Font License fonts) avoids the need for obfuscation altogether.

## Specifications

- [EPUB 3.3: Core media types — Fonts](https://www.w3.org/TR/epub-33/#cmt-grp-font)
- [EPUB 3.3: Exempt resources — Fonts](https://www.w3.org/TR/epub-33/#exempt-fonts)
- [EPUB 3.3: Resource locations](https://www.w3.org/TR/epub-33/#sec-resource-locations)
- [EPUB 3.3: Font obfuscation](https://www.w3.org/TR/epub-33/#sec-font-obfuscation)

## See also

- [Content directory](/en-US/docs/Related/EPUB/Structure/Content)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
- [The EPUB `META-INF` directory](/en-US/docs/Related/EPUB/Structure/META-INF)
- [`@font-face`](/en-US/docs/Web/CSS/Reference/At-rules/@font-face)
