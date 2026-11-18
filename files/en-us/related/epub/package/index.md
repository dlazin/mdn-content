---
title: EPUB package file
short-title: Overview
slug: Related/EPUB/Package
page-type: guide
sidebar: epub
---

The **package file** (also called the **package document** or **OPF file**, usually named `package.opf` or `content.opf`) is the [XML](/en-US/docs/Web/XML/Guides/XML_introduction) file that acts as the central index of an EPUB. It uses the media type `application/oebps-package+xml` and groups three required sections inside a root `<package>` element: [`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata), [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest), and [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<package xmlns="http://www.idpf.org/2007/opf"
         xmlns:dc="http://purl.org/dc/elements/1.1/"
         version="3.0"
         unique-identifier="pub-id"
         xml:lang="en">
  <metadata>
    <dc:identifier id="pub-id">urn:uuid:a1b2c3d4-e5f6-7890-abcd-ef1234567890</dc:identifier>
    <dc:title>Moby-Dick</dc:title>
    <dc:language>en</dc:language>
    <dc:creator>Herman Melville</dc:creator>
    <meta property="dcterms:modified">2023-05-25T12:00:00Z</meta>
  </metadata>

  <manifest>
    <item id="nav" href="nav.xhtml" media-type="application/xhtml+xml" properties="nav" />
    <item id="css" href="styles/main.css" media-type="text/css" />
    <item id="ch1" href="text/chapter-1.xhtml" media-type="application/xhtml+xml" />
    <item id="ch2" href="text/chapter-2.xhtml" media-type="application/xhtml+xml" />
  </manifest>

  <spine>
    <itemref idref="ch1" />
    <itemref idref="ch2" />
  </spine>
</package>
```

## Location and relative paths

The package file has no fixed path or filename. Reading systems find it through `<rootfile>` in [`META-INF/container.xml`](/en-US/docs/Related/EPUB/Structure/META-INF):

```xml
<rootfile full-path="OEBPS/package.opf" media-type="application/oebps-package+xml" />
```

Relative URLs inside the package file (such as `href` values in `<manifest>` and `<metadata>`) resolve from the directory containing the `.opf` file—for example, from `OEBPS/` when the file is at `OEBPS/package.opf`. Keep the package file unencrypted and place it at the top of your [content directory](/en-US/docs/Related/EPUB/Structure/Content) so all book files sit in the same folder or in subfolders inside it.

## Root `<package>` attributes

Every package file has a root `<package>` element in the `http://www.idpf.org/2007/opf` XML namespace. Because `<metadata>` uses Dublin Core (`dc:`) elements, `<package>` (or `<metadata>`) also declares `xmlns:dc="http://purl.org/dc/elements/1.1/"`.

- `xmlns`
  - : Required. Must be `"http://www.idpf.org/2007/opf"`.
- `version`
  - : Required. Must be `"3.0"` for all EPUB 3 books, including EPUB 3.2 and EPUB 3.3 (never `"3.2"` or `"3.3"`).
- `unique-identifier`
  - : Required. Matches the `id` attribute of the primary [`<dc:identifier>`](/en-US/docs/Related/EPUB/Package/Metadata) element inside `<metadata>`. This attribute holds the XML `id` (such as `"pub-id"`), not the ISBN or UUID string itself.
- `xml:lang`
  - : Optional. A [BCP 47](https://www.rfc-editor.org/info/bcp47) language tag (such as `en`, `fr`, or `ja`) for text strings inside the `.opf` file itself, such as `<dc:title>` and `<dc:description>`. This is separate from [`<dc:language>`](/en-US/docs/Related/EPUB/Package/Metadata), which is required in `<metadata>` to declare the language of the book's content.
- `dir`
  - : Optional. Base text direction (`ltr`, `rtl`, or `auto`; defaults to `auto`) for metadata strings in `<package>` and its children. Note that EPUB 3.3 marks `dir` in the package file as under-implemented because many reading systems do not apply it in library views.
- `prefix`
  - : Optional. Declares custom vocabulary prefixes for `property`, `properties`, and `rel` attributes as space-separated `prefix: url` pairs:

    ```xml
    <package xmlns="http://www.idpf.org/2007/opf"
             version="3.0"
             unique-identifier="pub-id"
             prefix="foaf: http://xmlns.com/foaf/spec/">
      <!-- metadata, manifest, and spine -->
    </package>
    ```

    EPUB 3 reserves `a11y:`, `dcterms:`, `marc:`, `media:`, `onix:`, `rendition:`, `schema:`, and `xsd:` automatically in package files, so you do not have to declare them in `prefix`.
- `id`
  - : Optional XML identifier for the `<package>` element.

## Child elements

Child elements of `<package>` must appear in the exact order shown here. Placing `<spine>` before `<manifest>`, or `<manifest>` before `<metadata>`, fails validation.

1. [`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata)
   - : Required (once). Contains Dublin Core metadata (`<dc:identifier>`, `<dc:title>`, `<dc:language>`, `<dc:creator>`, and others), `<meta>` properties, and `<link>` records.
2. [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest)
   - : Required (once). Lists every publication resource using `<item>` elements with `id`, `href`, `media-type`, and optional `properties` (such as `nav` and `cover-image`).
3. [`<spine>`](/en-US/docs/Related/EPUB/Package/Spine)
   - : Required (once). Lists `<itemref>` elements pointing to `<manifest>` item IDs in the book's default reading order.
4. `<guide>`
   - : Optional (at most once, legacy). An EPUB 2 list of structural `<reference>` links, replaced in EPUB 3 by [`<nav epub:type="landmarks">`](/en-US/docs/Related/EPUB/Navigation/Landmarks). EPUB 3 reading systems ignore `<guide>`.
5. `<bindings>`
   - : Optional (at most once, deprecated). An obsolete EPUB 3.0 element for scripted media handlers. Do not use it in new books.
6. `<collection>`
   - : Optional (zero or more). Groups related resources into sub-collections such as multi-file indexes or previews. Reading system support is minimal.

## Specifications

- [EPUB 3.3: Package document](https://www.w3.org/TR/epub-33/#sec-package-doc)
- [EPUB 3.3: The `package` element](https://www.w3.org/TR/epub-33/#sec-package-elem)
- [EPUB 3.3: Shared attributes](https://www.w3.org/TR/epub-33/#sec-shared-attrs)
- [EPUB 3.3: Package vocabulary association](https://www.w3.org/TR/epub-33/#sec-vocab-assoc)

## See also

- [EPUB package metadata](/en-US/docs/Related/EPUB/Package/Metadata)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
- [EPUB package spine](/en-US/docs/Related/EPUB/Package/Spine)
- [EPUB `META-INF` directory](/en-US/docs/Related/EPUB/Structure/META-INF)
- [EPUB content directory](/en-US/docs/Related/EPUB/Structure/Content)
