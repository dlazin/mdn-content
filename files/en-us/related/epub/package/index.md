---
title: EPUB package file
short-title: Overview
slug: Related/EPUB/Package
page-type: guide
sidebar: epub
---

The **package file** (also called the **package document** or **OPF file**, typically named `package.opf` or `content.opf`) is an [XML](/en-US/docs/Web/XML/Guides/XML_introduction) file that acts as the central index of an EPUB publication. It has the MIME media type `application/oebps-package+xml` and brings together three essential pieces of information inside a root **`<package>`** element:

1. **[`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata)**: Bibliographic and rendering metadata about the book (such as its title, unique identifier, language, author, and last-modified timestamp).
2. **[`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest)**: A complete inventory of every publication file in the [content directory](/en-US/docs/Related/EPUB/Structure/Content) (chapters, navigation document, stylesheets, images, fonts, and media).
3. **[`<spine>`](/en-US/docs/Related/EPUB/Package/Spine)**: An ordered list of content documents defining the default start-to-finish reading sequence.

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

## Location and path resolution

Unlike `META-INF/container.xml`, the package file does not have a fixed path or filename. Reading systems locate it by reading the `<rootfile>` element in [`META-INF/container.xml`](/en-US/docs/Related/EPUB/Structure/META-INF):

```xml
<rootfile full-path="OEBPS/package.opf" media-type="application/oebps-package+xml" />
```

All relative URLs inside the package file (such as `<item href="...">` in `<manifest>` and `<link href="...">` in `<metadata>`) resolve **relative to the directory containing the `.opf` file**—for example, relative to `OEBPS/` if the package file is at `OEBPS/package.opf`. Keep the package file unencrypted and place it at the top of your [content directory](/en-US/docs/Related/EPUB/Structure/Content) so all publication assets sit in the same folder or in subfolders beneath it.

## The `<package>` root element

Every package file has a single root `<package>` element in the Open Packaging Format XML namespace (`http://www.idpf.org/2007/opf`). Because `<metadata>` requires Dublin Core (`dc:`) elements, authors almost always declare `xmlns:dc="http://purl.org/dc/elements/1.1/"` on `<package>` as well.

### Required `<package>` attributes

- `xmlns`
  - : Must be `"http://www.idpf.org/2007/opf"`.
- `version`
  - : Specifies the major EPUB specification version. For all EPUB 3 publications—including EPUB 3.2 and EPUB 3.3—this value **must** be `"3.0"` (never `"3.2"` or `"3.3"`).
- `unique-identifier`
  - : An XML `IDREF` that matches the `id` attribute of the primary [`<dc:identifier>`](/en-US/docs/Related/EPUB/Package/Metadata) element inside `<metadata>`.

    Note that `unique-identifier` does **not** contain the ISBN or UUID itself; it contains the XML `id` (such as `"pub-id"`) that points to the `<dc:identifier id="pub-id">` element holding the actual identifier string.

### Optional `<package>` attributes

- `xml:lang`
  - : A [BCP 47](https://www.rfc-editor.org/info/bcp47) language tag (such as `en`, `en-US`, `fr`, or `ja`) declaring the language of text strings inside the `.opf` file itself—such as `<dc:title>`, `<dc:creator>`, and `<dc:description>`.

    Do not confuse `xml:lang` on `<package>` with [`<dc:language>`](/en-US/docs/Related/EPUB/Package/Metadata) inside `<metadata>`: `<dc:language>` is required and declares the language of the ebook's content, whereas `xml:lang` specifies the language of the metadata text in the package XML document (and can be overridden on individual metadata elements for multilingual metadata).
- `dir`
  - : Sets the base text directionality (`ltr`, `rtl`, or `auto`; defaults to `auto` if omitted) for human-readable metadata strings inside `<package>` and its descendants. Even though reading system support for bidirectional metadata display in library views is uneven, setting `dir="rtl"` (or overriding `dir` on specific `<dc:*>` / `<meta>` elements) is recommended when metadata is written in Arabic, Hebrew, or other right-to-left scripts.
- `prefix`
  - : Declares custom vocabulary prefixes for `property` attributes in `<metadata>`, `<manifest>`, and `<spine>`, formatted as whitespace-separated `prefix: uri` pairs:

    ```xml
    <package xmlns="http://www.idpf.org/2007/opf"
             version="3.0"
             unique-identifier="pub-id"
             prefix="prism: http://prismstandard.org/namespaces/basic/2.0/">
    ```

    You do **not** need a `prefix` attribute for EPUB's built-in reserved prefixes—`dcterms:`, `marc:`, `media:`, `onix:`, `rendition:`, `schema:`, and `xsd:` are pre-declared automatically in every EPUB 3 package file.
- `id`
  - : An optional XML identifier for the `<package>` element.

## Child elements and order

The children of `<package>` **must appear in the exact order listed here**. Putting `<spine>` before `<manifest>`, or `<manifest>` before `<metadata>`, is a validation error:

1. **[`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata)** _(required, exactly one)_: First child of `<package>`. Contains Dublin Core elements (`<dc:identifier>`, `<dc:title>`, `<dc:language>`, `<dc:creator>`, and others), `<meta>` property refinements (including `dcterms:modified`, layout settings, and accessibility metadata), and `<link>` records.
2. **[`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest)** _(required, exactly one)_: Second child of `<package>`. Lists every publication resource via `<item>` elements with `id`, `href`, `media-type`, and optional `properties` (such as `properties="nav"` and `properties="cover-image"`).
3. **[`<spine>`](/en-US/docs/Related/EPUB/Package/Spine)** _(required, exactly one)_: Third child of `<package>`. Lists `<itemref>` elements referencing `<manifest>` item IDs in the publication's default reading sequence.
4. **`<guide>`** _(optional, at most one — legacy)_: An EPUB 2 element containing `<reference>` links to structural sections (such as cover and start of text). Superseded in EPUB 3 by [`<nav epub:type="landmarks">`](/en-US/docs/Related/EPUB/Navigation/Landmarks) in the navigation document; EPUB 3 reading systems ignore `<guide>`.
5. **`<bindings>`** _(optional, at most one — deprecated)_: A deprecated early EPUB 3 element for associating custom scripted media handlers with foreign media types. Do not use it in new publications.
6. **`<collection>`** _(optional, zero or more)_: Groups related resources into logical sub-collections (for example, `role="index"` for multi-file indexes or `role="preview"` for book previews). Reading system support for `<collection>` is very limited in practice.

## Specifications

- [EPUB 3.3: Package document](https://www.w3.org/TR/epub-33/#sec-package-doc)
- [EPUB 3.3: The `package` element](https://www.w3.org/TR/epub-33/#sec-package-elem)
- [EPUB 3.3: Shared attributes](https://www.w3.org/TR/epub-33/#sec-shared-attrs)
- [EPUB 3.3: Package vocabulary association (`prefix` and reserved prefixes)](https://www.w3.org/TR/epub-33/#sec-vocab-assoc)

## See also

- [EPUB package metadata](/en-US/docs/Related/EPUB/Package/Metadata)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
- [EPUB package spine](/en-US/docs/Related/EPUB/Package/Spine)
- [The EPUB `META-INF` directory (`container.xml`)](/en-US/docs/Related/EPUB/Structure/META-INF)
- [EPUB content directory](/en-US/docs/Related/EPUB/Structure/Content)
