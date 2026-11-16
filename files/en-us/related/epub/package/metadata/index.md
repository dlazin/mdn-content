---
title: EPUB package metadata
short-title: Metadata
slug: Related/EPUB/Package/Metadata
page-type: guide
sidebar: epub
---

The **`<metadata>`** element is the first required child of the `<package>` element in an EPUB [package file](/en-US/docs/Related/EPUB/Package) (`.opf`). It contains bibliographic and publication metadata about the ebook—such as its title, unique identifier, language, and author—using elements from the [Dublin Core Metadata Element Set](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/) (`dc:`) along with EPUB `<meta>` and `<link>` elements.

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

- [`<meta property="dcterms:modified">`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/modified/)
  - : Specifies the date and time when the EPUB publication was last modified. Every EPUB `<metadata>` section must include exactly one `<meta property="dcterms:modified">` element that applies to the publication as a whole (without a `refines` attribute).

    The value must be a Coordinated Universal Time (UTC) timestamp formatted as `YYYY-MM-DDThh:mm:ssZ` (ending with a literal `Z` time zone indicator, with no fractional seconds or local time offsets):

    ```xml
    <meta property="dcterms:modified">2023-05-25T12:00:00Z</meta>
    ```

    Reading systems and distribution platforms combine the primary `<dc:identifier>` with this modification timestamp to distinguish between different revisions of the same ebook. Update this timestamp whenever you modify any file in the EPUB.

## Optional Dublin Core elements

In addition to the required elements, an EPUB `<metadata>` section can include optional Dublin Core elements to provide richer bibliographic information. Each element below can appear multiple times unless noted otherwise.

- [`<dc:contributor>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/contributor/)
  - : Specifies a person, organization, or service that made a secondary contribution to the publication—such as an editor, illustrator, translator, or narrator.

    ```xml
    <dc:contributor id="contrib-1">Rockwell Kent</dc:contributor>
    <meta refines="#contrib-1" property="role" scheme="marc:relators">ill</meta>
    ```

    Like `<dc:creator>`, if a publication has multiple contributors, list each in a separate `<dc:contributor>` element and optionally refine it with a `<meta property="role">` element to identify the contributor's specific role.

- [`<dc:creator>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/creator/)
  - : Specifies a primary creator of the publication, typically the author.

    ```xml
    <dc:creator>Herman Melville</dc:creator>
    ```

    If an EPUB publication has more than one creator, specify each in a separate `<dc:creator>` element. The document order of `<dc:creator>` elements in the `<metadata>` section determines their display priority, with the first `<dc:creator>` element treated as the primary creator.

    > [!NOTE]
    > Many reading systems display only the first `<dc:creator>` element.

- [`<dc:date>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/date/)
  - : Specifies the publication date of the EPUB. Unlike other optional Dublin Core elements, `<metadata>` may contain at most one `<dc:date>` element.

    Format the value using an [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) date or UTC timestamp—most commonly `YYYY-MM-DD`, `YYYY-MM`, or `YYYY`:

    ```xml
    <dc:date>1851-10-18</dc:date>
    ```

    Do not use `<dc:date>` for the last modification date of the EPUB file; use the required `<meta property="dcterms:modified">` element for modification timestamps.

- [`<dc:subject>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/subject/)
  - : Specifies a topic, keyword, or subject classification for the publication. Use a separate `<dc:subject>` element for each subject.

    ```xml
    <dc:subject>Whaling -- Fiction</dc:subject>
    ```

    To associate a `<dc:subject>` element with a standardized subject taxonomy (such as BISAC or Thema), give the `<dc:subject>` an `id` and refine it with `<meta>` elements using the `authority` and `term` properties:

    ```xml
    <dc:subject id="subj-1">FICTION / Classics</dc:subject>
    <meta refines="#subj-1" property="authority">BISAC</meta>
    <meta refines="#subj-1" property="term">FIC004000</meta>
    ```

- [`<dc:type>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/type/)
  - : Specifies the nature, genre, or specialized functional type of the publication (for example, `dictionary`, `index`, or `preview`).

    ```xml
    <dc:type>dictionary</dc:type>
    ```

## Examples

### Minimal package metadata section

The following example shows a minimal `<metadata>` section inside a package file, containing the four required metadata elements alongside the optional `<dc:creator>` element:

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
- [EPUB 3.3: DCMES optional elements](https://www.w3.org/TR/epub-33/#sec-opf-dcmes-optional-def)
- [EPUB 3.3: The `dc:contributor` element](https://www.w3.org/TR/epub-33/#sec-opf-dccontributor)
- [EPUB 3.3: The `dc:creator` element](https://www.w3.org/TR/epub-33/#sec-opf-dccreator)
- [EPUB 3.3: The `dc:date` element](https://www.w3.org/TR/epub-33/#sec-opf-dcdate)
- [EPUB 3.3: The `dc:subject` element](https://www.w3.org/TR/epub-33/#sec-opf-dcsubject)
- [EPUB 3.3: The `dc:type` element](https://www.w3.org/TR/epub-33/#sec-opf-dctype)
- [EPUB 3.3: The `meta` element](https://www.w3.org/TR/epub-33/#sec-meta-elem)

## See also

- [Package file](/en-US/docs/Related/EPUB/Package)
- [EPUB structure](/en-US/docs/Related/EPUB/Structure)
