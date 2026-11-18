---
title: EPUB package metadata
short-title: Metadata
slug: Related/EPUB/Reference/Package/Metadata
page-type: guide
sidebar: epub
---

The `<metadata>` element is the first child of `<package>` in an EPUB [package file](/en-US/docs/Related/EPUB/Reference/Package). It describes the book—such as its title, identifier, language, and author—using [Dublin Core](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/) (`dc:`) elements alongside EPUB `<meta>` and `<link>` elements.

To use Dublin Core elements, declare `xmlns:dc="http://purl.org/dc/elements/1.1/"` on `<package>` or `<metadata>`.

## Required elements

Every metadata section must include `<dc:identifier>`, `<dc:title>`, `<dc:language>`, and `<meta property="dcterms:modified">`. Child elements inside `<metadata>` can appear in any order.

- [`<dc:identifier>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/identifier/)
  - : A unique identifier for the book, such as an ISBN, DOI, URL, or [UUID](/en-US/docs/Glossary/UUID). At least one `<dc:identifier>` is required.

    A book can list multiple `<dc:identifier>` elements. One must carry an `id` matching the `unique-identifier` attribute on `<package>`, marking it as the primary identifier:

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

    If your book does not have an assigned industry identifier such as an ISBN (`urn:isbn:...`) or DOI (`urn:doi:...`), generate a UUID prefixed with `urn:uuid:`. Keep the primary identifier stable across minor updates and errata fixes.

- [`<dc:title>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/title/)
  - : The title of the book. At least one `<dc:title>` is required.

    ```xml
    <dc:title>Moby-Dick; or, The Whale</dc:title>
    ```

    Reading systems treat the first `<dc:title>` in document order as the main title. Although the specification allows multiple `<dc:title>` elements (for example, refined with `title-type`), EPUB 3.3 recommends putting the full title in a single `<dc:title>` element because many reading systems ignore additional `<dc:title>` elements.

- [`<dc:language>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/language/)
  - : The language of the book's content, written as a [BCP 47](https://www.rfc-editor.org/info/bcp47) language tag (such as `en`, `en-US`, `fr`, or `ja`). At least one `<dc:language>` is required. Reading systems use it to select default fonts, hyphenation rules, and text-to-speech voices.

    ```xml
    <dc:language>en</dc:language>
    ```

    For multilingual books, include a `<dc:language>` element for each language, placing the primary language first.

- [`<meta property="dcterms:modified">`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/modified/)
  - : The UTC date and time when the EPUB was last modified. Every metadata section must include one unrefined `<meta property="dcterms:modified">` element formatted as `YYYY-MM-DDThh:mm:ssZ` (with a literal `Z` and no fractional seconds):

    ```xml
    <meta property="dcterms:modified">2023-05-25T12:00:00Z</meta>
    ```

    Reading systems and bookstores combine `<dc:identifier>` with this timestamp to distinguish revisions of the same ebook. Update it whenever you change any file in the EPUB.

## Optional Dublin Core elements

Package metadata can also include any of the other 12 elements from the Dublin Core Metadata Element Set. Each can repeat unless noted otherwise.

- [`<dc:contributor>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/contributor/)
  - : A person or organization that made a secondary contribution, such as an editor, illustrator, translator, or narrator. List each contributor in its own element, optionally refined with `<meta property="role">`:

    ```xml
    <dc:contributor id="contrib-1">Rockwell Kent</dc:contributor>
    <meta refines="#contrib-1" property="role" scheme="marc:relators">ill</meta>
    ```

- [`<dc:coverage>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/coverage/)
  - : The geographic area, time period, or jurisdiction covered by the work.

    ```xml
    <dc:coverage>19th century</dc:coverage>
    ```

- [`<dc:creator>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/creator/)
  - : A primary creator of the work, usually the author. List multiple creators in separate `<dc:creator>` elements in display order. Many reading systems show only the first `<dc:creator>` in library views.

    ```xml
    <dc:creator>Herman Melville</dc:creator>
    ```

- [`<dc:date>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/date/)
  - : The publication date of the EPUB (at most one `<dc:date>` is allowed). Format it as an [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) string such as `YYYY-MM-DD`, `YYYY-MM`, or `YYYY`. Use `<meta property="dcterms:modified">`, not `<dc:date>`, for the file's last-modified timestamp.

    ```xml
    <dc:date>1851-10-18</dc:date>
    ```

- [`<dc:description>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/description/)
  - : A plain-text summary or blurb for the book, often shown on bookstore and library detail pages.

    ```xml
    <dc:description>Ishmael recounts Captain Ahab's obsessive voyage aboard the whaling ship Pequod.</dc:description>
    ```

- [`<dc:format>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/format/)
  - : The file format or physical medium of the resource. Rarely needed in EPUB because [`mimetype`](/en-US/docs/Related/EPUB/Reference/Structure/mimetype) already declares `application/epub+zip`.

- [`<dc:publisher>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/publisher/)
  - : The publisher, organization, or imprint making the book available.

    ```xml
    <dc:publisher>Richard Bentley</dc:publisher>
    ```

- [`<dc:relation>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/relation/)
  - : A related resource, such as a companion website or related work.

- [`<dc:rights>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/rights/)
  - : A copyright statement or license notice.

    ```xml
    <dc:rights>Public domain in the United States.</dc:rights>
    ```

- [`<dc:source>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/source/)
  - : A prior work from which the EPUB was derived, such as the ISBN of a source print edition. (To identify the source of static page breaks for accessibility, see [`pageBreakSource` in EPUB page list](/en-US/docs/Related/EPUB/Reference/Navigation/Page-list#marking_page_breaks_in_content_documents).)

    ```xml
    <dc:source>urn:isbn:9780142437247</dc:source>
    ```

- [`<dc:subject>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/subject/)
  - : A topic or subject heading. Use a separate `<dc:subject>` element for each subject. To attach a standard subject code (such as BISAC or Thema), refine `<dc:subject>` with both `authority` and `term`:

    ```xml
    <dc:subject id="subj-1">FICTION / Classics</dc:subject>
    <meta refines="#subj-1" property="authority">BISAC</meta>
    <meta refines="#subj-1" property="term">FIC004000</meta>
    ```

- [`<dc:type>`](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/type/)
  - : The nature, genre, or specialized type of the publication (such as `dictionary` or `preview`).

## `<meta>` and `<link>` elements

In addition to Dublin Core elements, package metadata supports `<meta>` and `<link>` in the `http://www.idpf.org/2007/opf` namespace:

- `<meta>`
  - : Defines a metadata property for the whole book or refines another element:
    - `property`: Required. The property name, either from the default [EPUB meta properties vocabulary](https://www.w3.org/TR/epub-33/#app-vocabs) (such as `file-as`, `role`, `belongs-to-collection`, or `pageBreakSource`) or with a vocabulary prefix (such as `dcterms:modified`, `rendition:layout`, or `schema:accessibilitySummary`).
    - `refines`: Optional. A fragment identifier (such as `#creator-1`) pointing to the `id` of another metadata element or manifest item that this `<meta>` describes.
    - `scheme`: Optional. The prefixed vocabulary or code list used for the element's value (such as `marc:relators` or `onix:codelist5`).

    ```xml
    <dc:creator id="creator-1">Herman Melville</dc:creator>
    <meta refines="#creator-1" property="file-as">Melville, Herman</meta>
    <meta refines="#creator-1" property="role" scheme="marc:relators">aut</meta>
    ```

- `<link>`
  - : Links an external or in-archive metadata resource (such as an ONIX, MARC, or JSON-LD record, or a spoken title audio clip) to the book:
    - `href`: Required. The URL or path (relative to the package file) of the linked resource.
    - `rel`: Required. Space-separated relationship tokens (such as `record`, `alternate`, `voicing`, or `xml-signature`).
    - `media-type`: Required when linking to a file inside the EPUB archive; optional for external URLs.
    - `hreflang`: Optional [BCP 47](https://www.rfc-editor.org/info/bcp47) language tag for the linked resource.
    - `refines`: Optional fragment identifier (`#id`) when the link describes a specific element (required for `rel="voicing"`, and not allowed with `rel="record"`).
    - `properties`: Optional space-separated tokens identifying the record format (such as `onix` when `media-type` is generic `application/xml`).

    ```xml
    <link rel="record" href="meta/onix.xml" media-type="application/xml" properties="onix" />
    ```

    Standalone metadata files referenced by `<link>` inside the archive must be stored outside `META-INF` and must not be listed in the [manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest).

## Examples

### Minimal metadata

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

### Richer metadata with refinements and accessibility fields

```xml
<package xmlns="http://www.idpf.org/2007/opf"
         xmlns:dc="http://purl.org/dc/elements/1.1/"
         version="3.0"
         unique-identifier="pub-id"
         xml:lang="en">
  <metadata>
    <dc:identifier id="pub-id">urn:isbn:9780142437247</dc:identifier>
    <dc:title>Moby-Dick; or, The Whale</dc:title>
    <dc:language>en-US</dc:language>

    <dc:creator id="author">Herman Melville</dc:creator>
    <meta refines="#author" property="file-as">Melville, Herman</meta>
    <meta refines="#author" property="role" scheme="marc:relators">aut</meta>

    <dc:contributor id="illustrator">Rockwell Kent</dc:contributor>
    <meta refines="#illustrator" property="role" scheme="marc:relators">ill</meta>

    <dc:publisher>Penguin Classics</dc:publisher>
    <dc:date>1851-10-18</dc:date>
    <dc:description>Ishmael recounts Captain Ahab's obsessive voyage aboard the whaling ship Pequod.</dc:description>

    <dc:subject id="subj-1">FICTION / Classics</dc:subject>
    <meta refines="#subj-1" property="authority">BISAC</meta>
    <meta refines="#subj-1" property="term">FIC004000</meta>

    <dc:rights>Public domain in the United States.</dc:rights>

    <meta property="schema:accessMode">textual</meta>
    <meta property="schema:accessMode">visual</meta>
    <meta property="schema:accessibilityFeature">alternativeText</meta>
    <meta property="schema:accessibilitySummary">This publication includes structural navigation and image descriptions.</meta>

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
- [EPUB 3.3: The `meta` element](https://www.w3.org/TR/epub-33/#sec-meta-elem)
- [EPUB 3.3: The `link` element](https://www.w3.org/TR/epub-33/#sec-link-elem)
- [EPUB Accessibility 1.1](https://www.w3.org/TR/epub-a11y-11/)
