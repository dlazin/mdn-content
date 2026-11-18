---
title: EPUB fonts
short-title: Fonts
slug: Related/EPUB/Reference/Content/Fonts
page-type: guide
sidebar: epub
---

By default, EPUB reading systems render text with built-in fonts and let readers switch typefaces to suit their preferences. When a book needs custom typography—such as display fonts for headings, mathematical or phonetic glyphs, or scripts not preinstalled on every device—you can bundle font files in the [content directory](/en-US/docs/Related/EPUB/Reference/Structure/Content), declare them in the [manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest), and load them in CSS with [`@font-face`](/en-US/docs/Web/CSS/Reference/At-rules/@font-face).

## Supported font formats

EPUB 3 defines four font [core media types](/en-US/docs/Related/EPUB/Reference/Content/Core_media_types):

| Format            | Preferred `media-type` | Legacy `media-type` aliases                            | CSS `format()` value |
| ----------------- | ---------------------- | ------------------------------------------------------ | -------------------- |
| WOFF2 (`.woff2`)  | `font/woff2`           | None                                                   | `"woff2"`            |
| WOFF (`.woff`)    | `font/woff`            | `application/font-woff`                                | `"woff"`             |
| OpenType (`.otf`) | `font/otf`             | `application/font-sfnt`, `application/vnd.ms-opentype` | `"opentype"`         |
| TrueType (`.ttf`) | `font/ttf`             | `application/font-sfnt`                                | `"truetype"`         |

WOFF2 (`font/woff2`) produces the smallest EPUB files thanks to built-in compression, while OpenType (`font/otf`) and TrueType (`font/ttf`) are also recognized by older e-readers. Other font formats are [exempt resources](/en-US/docs/Related/EPUB/Reference/Content/Core_media_types#exempt_resources) because CSS `font-family` stacks fall back to a system font automatically.

## Embedding and styling fonts

To embed custom fonts in an EPUB:

1. Place the font files in your [content directory](/en-US/docs/Related/EPUB/Reference/Structure/Content) (for example, `OEBPS/fonts`) and declare each file in the [manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest):

   ```xml
   <manifest>
     <item id="font-regular" href="fonts/literata-regular.woff2" media-type="font/woff2" />
     <item id="font-italic" href="fonts/literata-italic.woff2" media-type="font/woff2" />
     <item id="font-bold" href="fonts/literata-bold.woff2" media-type="font/woff2" />
     <item id="css" href="styles/main.css" media-type="text/css" />
     <!-- other manifest items -->
   </manifest>
   ```

2. Define [`@font-face`](/en-US/docs/Web/CSS/Reference/At-rules/@font-face) rules in CSS using paths relative to the stylesheet, and include fallback families in every `font-family` rule:

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

Although EPUB 3 permits loading fonts from remote URLs when flagged with [`properties="remote-resources"`](/en-US/docs/Related/EPUB/Reference/Package/Manifest#manifest_item_properties), bundling font files inside the container is strongly preferred so typography works offline.

## Font licensing and obfuscation

Because an EPUB is a ZIP archive, anyone can unzip it and extract unencrypted files. Before embedding a commercial font, confirm that its license covers ebook distribution—standard desktop and webfont licenses often do not.

Some commercial foundries permit EPUB embedding only if the font file is obfuscated so it cannot be casually copied and installed elsewhere. Prefer open-licensed fonts, WOFF2 compression, or glyph subsetting whenever your license allows, because reading systems are not required to support font deobfuscation. For the SHA-1 obfuscation algorithm and how to declare obfuscated fonts in `META-INF/encryption.xml`, see [Font obfuscation in EPUB `encryption.xml`](/en-US/docs/Related/EPUB/Reference/Structure/encryption.xml#font_obfuscation).

## Specifications

- [EPUB 3.3: Core media types — Fonts](https://www.w3.org/TR/epub-33/#cmt-grp-font)
- [EPUB 3.3: Exempt resources — Fonts](https://www.w3.org/TR/epub-33/#exempt-fonts)
- [EPUB 3.3: Resource locations](https://www.w3.org/TR/epub-33/#sec-resource-locations)

## See also

- [EPUB `encryption.xml` file](/en-US/docs/Related/EPUB/Reference/Structure/encryption.xml)
- [EPUB content directory](/en-US/docs/Related/EPUB/Reference/Structure/Content)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest)
- [`@font-face`](/en-US/docs/Web/CSS/Reference/At-rules/@font-face)
