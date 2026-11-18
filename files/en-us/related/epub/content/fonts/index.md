---
title: EPUB fonts
short-title: Fonts
slug: Related/EPUB/Content/Fonts
page-type: guide
sidebar: epub
---

By default, EPUB reading systems render text with built-in fonts and let readers switch typefaces to suit their preferences. When a book needs custom typography—such as display fonts for headings, mathematical or phonetic glyphs, or scripts not preinstalled on every device—you can bundle font files in the [content directory](/en-US/docs/Related/EPUB/Structure/Content), declare them in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest), and load them in CSS with [`@font-face`](/en-US/docs/Web/CSS/Reference/At-rules/@font-face).

## Supported font formats and media types

EPUB 3 defines four font [core media types](/en-US/docs/Related/EPUB/Content/Core_media_types) that visual reading systems support without fallbacks:

| Format            | Preferred `media-type` | Legacy `media-type` aliases                            | CSS `format()` value |
| ----------------- | ---------------------- | ------------------------------------------------------ | -------------------- |
| WOFF2 (`.woff2`)  | `font/woff2`           | None                                                   | `"woff2"`            |
| WOFF (`.woff`)    | `font/woff`            | `application/font-woff`                                | `"woff"`             |
| OpenType (`.otf`) | `font/otf`             | `application/font-sfnt`, `application/vnd.ms-opentype` | `"opentype"`         |
| TrueType (`.ttf`) | `font/ttf`             | `application/font-sfnt`                                | `"truetype"`         |

Use the preferred `font/*` media types (`font/woff2`, `font/woff`, `font/otf`, or `font/ttf`) in new EPUB 3 files. The `application/*` values are legacy aliases kept for backward compatibility.

WOFF2 (`font/woff2`) produces the smallest `.epub` files thanks to built-in compression, while OpenType (`font/otf`) and TrueType (`font/ttf`) are also recognized by older e-readers. Any font format outside these four core media types is an *exempt resource* in EPUB 3: it does not require a manifest `fallback` attribute because CSS `font-family` stacks fall back to a system font automatically.

## Embedding and styling fonts

To embed custom fonts in an EPUB:

1. Place the font files in your [content directory](/en-US/docs/Related/EPUB/Structure/Content) (for example, `OEBPS/fonts/`).
2. Declare each font file as an `<item>` in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest):

   ```xml
   <manifest>
     <item id="font-regular" href="fonts/literata-regular.woff2" media-type="font/woff2" />
     <item id="font-italic" href="fonts/literata-italic.woff2" media-type="font/woff2" />
     <item id="font-bold" href="fonts/literata-bold.woff2" media-type="font/woff2" />
     <item id="css" href="styles/main.css" media-type="text/css" />
     <!-- other manifest items -->
   </manifest>
   ```

3. Define [`@font-face`](/en-US/docs/Web/CSS/Reference/At-rules/@font-face) rules in CSS using paths relative to the stylesheet, and include fallback families in every `font-family` rule:

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
> Many reading systems let readers override body fonts or ignore embedded body fonts unless the user selects "Publisher Default." Avoid `!important` on `font-family`, and verify that your book remains readable when switched to a built-in device font.

### Remotely hosted fonts

EPUB 3 permits hosting font files outside the `.epub` container (using `https:` URLs is strongly recommended, as reading systems may block insecure `http:` requests). If a stylesheet or XHTML document references a remote font:

- Declare the remote font URL as an `<item>` in `<manifest>`.
- Add `properties="remote-resources"` to the `<manifest>` item for the stylesheet or content document that references the font.

Because ebooks are often read offline, bundling fonts inside the container is more reliable than loading remote web fonts.

## Font licensing and obfuscation

Because an `.epub` file is a ZIP archive, anyone can unzip it and extract unencrypted files. Before embedding a commercial font, confirm that its license covers ebook distribution—standard desktop and webfont licenses often do not.

Some commercial foundries permit EPUB embedding only if the font is tied to the publication so it cannot be casually copied and installed elsewhere. Use **font obfuscation** only when no simpler option—such as open-licensed fonts, WOFF2 compression, or glyph subsetting—satisfies your font license:

1. Strip all XML whitespace characters (`U+0020`, `U+0009`, `U+000D`, and `U+000A`) from the publication's primary [`<dc:identifier>`](/en-US/docs/Related/EPUB/Package/Metadata) (the one referenced by `unique-identifier` on `<package>`) and compute the 20-byte SHA-1 digest of its UTF-8 bytes.
2. Before compressing the font into the ZIP archive, XOR the first 1,040 bytes of the raw font file against the repeating 20-byte SHA-1 key, leaving the rest of the file unchanged.
3. List each obfuscated font in [`META-INF/encryption.xml`](/en-US/docs/Related/EPUB/Structure/META-INF) with `Algorithm="http://www.idpf.org/2008/embedding"` and a `URI` relative to the container root:

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

Declare every obfuscated font in `<manifest>` with its normal font core media type (`font/woff2`, `font/woff`, `font/otf`, or `font/ttf`, or a legacy alias), and do not include the obfuscation key in `encryption.xml`.

Font obfuscation has two important limits:

- It only deters casual extraction from an unzipped `.epub` archive; because the key comes from the public `<dc:identifier>` in the package file, it is not cryptographic protection.
- Reading systems are not required to support font deobfuscation. Non-supporting readers ignore obfuscated fonts and fall back to the next typeface in your CSS `font-family` stack.

## Specifications

- [EPUB 3.3: Core media types — Fonts](https://www.w3.org/TR/epub-33/#cmt-grp-font)
- [EPUB 3.3: Exempt resources — Fonts](https://www.w3.org/TR/epub-33/#exempt-fonts)
- [EPUB 3.3: Resource locations](https://www.w3.org/TR/epub-33/#sec-resource-locations)
- [EPUB 3.3: Font obfuscation](https://www.w3.org/TR/epub-33/#sec-font-obfuscation)

## See also

- [EPUB content directory](/en-US/docs/Related/EPUB/Structure/Content)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
- [EPUB `META-INF` directory](/en-US/docs/Related/EPUB/Structure/META-INF)
- [`@font-face`](/en-US/docs/Web/CSS/Reference/At-rules/@font-face)
