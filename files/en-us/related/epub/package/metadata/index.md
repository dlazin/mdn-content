---
title: EPUB package metadata
short-title: Metadata
slug: Related/EPUB/Package/Metadata
page-type: guide
tags:
  - EPUB
  - Ebook
  - Ebooks
  - Reference
---
{{EPUBRef}}

The **`<metadata>`** element is the first required child of the `<package>` element in an EPUB [package file](/en-US/docs/Related/EPUB/Package) (`.opf`). It contains bibliographic and publication metadata about the ebook—such as its title, unique identifier, language, and author—using elements from the [Dublin Core Metadata Element Set](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/) (`dc:`) along with EPUB `<meta>` elements.

To use Dublin Core elements in `<metadata>`, the `<package>` element (or the `<metadata>` element itself) must declare the Dublin Core namespace: `xmlns:dc="http://purl.org/dc/elements/1.1/"`.

## Required elements

Every EPUB `<metadata>` section must include `<dc:identifier>`, `<dc:title>`, and `<dc:language>`, as well as a `<meta property="dcterms:modified">` element.

- [`<dc:title>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/title/)
  - : Specifies the title of the publication. Every EPUB must include at least one `<dc:title>` element.

    ```xml
    <dc:title>Moby-Dick</dc:title>
    ```

    If a publication has multiple `<dc:title>` elements (for example, a main title and a subtitle), reading systems treat the first `<dc:title>` element in document order as the primary title unless a `<meta>` element with the `title-type` property specifies otherwise.

## Optional Dublin Core elements

In addition to the required elements, an EPUB `<metadata>` section can include optional Dublin Core elements to provide richer bibliographic information.

- [`<dc:creator>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/creator/)
  - : Specifies a primary creator of the publication, typically the author.

    ```xml
    <dc:creator>Herman Melville</dc:creator>
    ```

    If an EPUB publication has more than one creator, specify each in a separate `<dc:creator>` element. The document order of `<dc:creator>` elements in the `<metadata>` section determines their display priority, with the first `<dc:creator>` element treated as the primary creator.

    > [!NOTE]
    > Many reading systems display only the first `<dc:creator>` element.

## Examples

### Minimal package metadata section

The following example shows a minimal `<metadata>` section inside a package file, including `<dc:title>` and `<dc:creator>` alongside the other required metadata fields:

```xml
<package xmlns="http://www.idpf.org/2007/opf"
         xmlns:dc="http://purl.org/dc/elements/1.1/"
         version="3.0"
         unique-identifier="pub-id">
  <metadata>
    <dc:identifier id="pub-id">urn:uuid:a1b2c3d4-e5f6-7890-abcd-ef1234567890</dc:identifier>
    <dc:title>Moby-Dick</dc:title>
    <dc:language>en</dc:language>
    <dc:creator>Herman Melville</dc:creator>
    <meta property="dcterms:modified">2023-05-25T12:00:00Z</meta>
  </metadata>
  <!-- <manifest> and <spine> follow -->
</package>
```

## Specifications

- [EPUB 3.3: The `metadata` element](https://www.w3.org/TR/epub-33/#sec-metadata-elem)
- [EPUB 3.3: The `dc:title` element](https://www.w3.org/TR/epub-33/#sec-opf-dctitle)
- [EPUB 3.3: The `dc:creator` element](https://www.w3.org/TR/epub-33/#sec-opf-dccreator)

## See also

- [Package file](/en-US/docs/Related/EPUB/Package)
- [EPUB structure](/en-US/docs/Related/EPUB/Structure)
