---
title: EPUB package metadata
short-title: Metadata
slug: Related/EPUB/Package/Metadata
page-type: guide
sidebar: epub
---

The **`<metadata>`** element is the first required child of the `<package>` element in an EPUB [package file](/en-US/docs/Related/EPUB/Package) (`.opf`). It contains bibliographic and publication metadata about the ebook—such as its title, unique identifier, language, and author—using elements from the [Dublin Core Metadata Element Set](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/) (`dc:`) along with EPUB `<meta>` elements.

To use Dublin Core elements in `<metadata>`, the `<package>` element (or the `<metadata>` element itself) must declare the Dublin Core namespace: `xmlns:dc="http://purl.org/dc/elements/1.1/"`.

## Required elements

Every EPUB `<metadata>` section must include `<dc:identifier>`, `<dc:title>`, and `<dc:language>`, as well as a `<meta property="dcterms:modified">` element.

- [`<dc:identifier>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/identifier/)
  - : Specifies a unique identifier for the publication, such as an ISBN, DOI, URL, or [UUID](/en-US/docs/Glossary/UUID). Every EPUB must include at least one `<dc:identifier>` element.

    An EPUB can include multiple `<dc:identifier>` elements (for example, both an ISBN and an internal publisher ID). One `<dc:identifier>` element must have an `id` attribute whose value matches the `unique-identifier` attribute on the root `<package>` element, designating it as the primary identifier for the publication:

    ```xml
    <package xmlns="http://www.idpf.org/2007/opf"
             xmlns:dc="http://purl.org/dc/elements/1.1/"
             version="3.0"
             unique-identifier="pub-id">
      <metadata>
        <dc:identifier id="pub-id">urn:uuid:a1b2c3d4-e5f6-7890-abcd-ef1234567890</dc:identifier>
        <!-- ... -->
      </metadata>
    </package>
    ```

    The primary identifier is intended to be unique across all ebooks worldwide. If your publication does not have an assigned industry identifier such as an ISBN (`urn:isbn:...`) or DOI (`doi:...`), generate a UUID prefixed with `urn:uuid:`.

- [`<dc:title>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/title/)
  - : Specifies the title of the publication. Every EPUB must include at least one `<dc:title>` element.

    ```xml
    <dc:title>Moby-Dick</dc:title>
    ```

    If a publication has multiple `<dc:title>` elements (for example, a main title and a subtitle), reading systems treat the first `<dc:title>` element in document order as the primary title unless a `<meta>` element with the `title-type` property specifies otherwise.

- [`<dc:language>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/language/)
  - : Specifies the language of the publication's content. Every EPUB must include at least one `<dc:language>` element.

    The value must be a valid [BCP 47](https://www.rfc-editor.org/info/bcp47) language tag (such as `en` for English, `en-US` for American English, `fr` for French, or `ja` for Japanese). Reading systems use this value to select default fonts, hyphenation rules, and text-to-speech voices.

    ```xml
    <dc:language>en</dc:language>
    ```

    If a publication contains content in multiple languages, you can include multiple `<dc:language>` elements; the first `<dc:language>` element in document order represents the primary language of the publication.

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

The following example shows a minimal `<metadata>` section inside a package file, including `<dc:identifier>`, `<dc:title>`, `<dc:language>`, and `<dc:creator>` alongside `<meta property="dcterms:modified">`:

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
- [EPUB 3.3: The `dc:identifier` element](https://www.w3.org/TR/epub-33/#sec-opf-dcidentifier)
- [EPUB 3.3: The `dc:title` element](https://www.w3.org/TR/epub-33/#sec-opf-dctitle)
- [EPUB 3.3: The `dc:language` element](https://www.w3.org/TR/epub-33/#sec-opf-dclanguage)
- [EPUB 3.3: The `dc:creator` element](https://www.w3.org/TR/epub-33/#sec-opf-dccreator)

## See also

- [Package file](/en-US/docs/Related/EPUB/Package)
- [EPUB structure](/en-US/docs/Related/EPUB/Structure)
