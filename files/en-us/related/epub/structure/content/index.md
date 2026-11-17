---
title: EPUB content directory
short-title: Content directory
slug: Related/EPUB/Structure/Content
page-type: guide
sidebar: epub
---

In an unzipped EPUB, the **content directory** (often named `OEBPS`, `OPS`, or `EPUB`) is the folder that holds the ebook's [package file](/en-US/docs/Related/EPUB/Package) (`.opf`), [navigation document](/en-US/docs/Related/EPUB/Navigation) (`nav.xhtml`), and all [publication content](/en-US/docs/Related/EPUB/Content)—including XHTML chapters, CSS stylesheets, images, fonts, audio, video, and scripts:

```plain
book.epub/
├── mimetype
├── META-INF/
│   └── container.xml
└── OEBPS/
    ├── package.opf
    ├── nav.xhtml
    ├── fonts/
    │   └── literata-regular.woff2
    ├── images/
    │   ├── cover.jpg
    │   └── map.png
    ├── styles/
    │   └── main.css
    └── text/
        ├── cover.xhtml
        ├── chapter-1.xhtml
        └── chapter-2.xhtml
```

## Naming and directory requirements

Unlike [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype) and [`META-INF`](/en-US/docs/Related/EPUB/Structure/META-INF), whose exact names and root locations are mandated by the [Open Container Format (OCF)](/en-US/docs/Related/EPUB/Structure/OCF), **the EPUB specification does not require a specific name for the content directory**:

- **Common naming conventions**: Authoring tools most commonly name this folder `OEBPS` (_Open eBook Publication Structure_), `OPS` (_Open Publication Structure_), or `EPUB`. You can choose any valid folder name—as long as the `full-path` attribute on `<rootfile>` inside [`META-INF/container.xml`](/en-US/docs/Related/EPUB/Structure/META-INF) points to the exact path of your `.opf` package file (for example, `full-path="OEBPS/package.opf"`).
- **Why use a single content directory**: Technically, OCF permits placing `package.opf` and content files directly in the root directory of the container (as long as nothing is placed inside `META-INF` or named `mimetype`). In practice, however, placing `package.opf` at the top of a dedicated content directory—and keeping every publication resource inside or in subfolders beneath that directory—is strongly recommended:
  - It cleanly separates container files (`mimetype` and `META-INF/`) from book files.
  - Some reading systems restrict access to files located in parent or sibling directories outside the folder that contains `package.opf`. Keeping `package.opf` at the top of the content directory (such as `OEBPS/package.opf` rather than `OEBPS/metadata/package.opf`) ensures every asset lives at or below the package file's directory.

Similarly, subfolders inside the content directory (such as `text/`, `styles/`, `images/`, `fonts/`, and `audio/`) are optional organizational conventions. Small ebooks sometimes keep all files flat inside `OEBPS/`, while larger publications group files by asset type.

## Every content file must be in the manifest

Every publication resource stored in the content directory **must** be declared as an `<item>` in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest). Leaving unused images, backup files, or hidden operating-system metadata files (such as `.DS_Store` or `Thumbs.db`) inside the content directory without a matching `<manifest>` entry causes [EPUBCheck](https://www.w3.org/publishing/epubcheck/) validation errors and retailer upload rejections.

## Relative paths across an EPUB

One of the most common sources of broken links and missing images in hand-edited EPUBs is mixing up base directories when writing relative paths. Depending on which file you are editing, relative URLs resolve from one of **three different base locations**:

| File containing the path                                                                                | Path is relative to                                                   | Example target: `OEBPS/images/cover.jpg`                               |
| ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| Files in [`META-INF/`](/en-US/docs/Related/EPUB/Structure/META-INF) (`container.xml`, `encryption.xml`) | **Container root directory**                                          | `OEBPS/package.opf` (or `OEBPS/fonts/display.otf` in `encryption.xml`) |
| [Package file](/en-US/docs/Related/EPUB/Package) (`OEBPS/package.opf`)                                  | **Directory containing `package.opf`** (`OEBPS/`)                     | `images/cover.jpg`                                                     |
| Content files (`OEBPS/text/cover.xhtml` or `OEBPS/styles/main.css`)                                     | **Directory containing that file** (`OEBPS/text/` or `OEBPS/styles/`) | `../images/cover.jpg`                                                  |

For example, given the directory tree at the top of this page:

1. **`META-INF/container.xml`** resolves from the container root, so it includes the `OEBPS/` folder name:

   ```xml
   <rootfile full-path="OEBPS/package.opf" media-type="application/oebps-package+xml" />
   ```

2. **`OEBPS/package.opf`** already lives inside `OEBPS/`, so `<manifest>` paths omit `OEBPS/`:

   ```xml
   <item id="cover-img" href="images/cover.jpg" media-type="image/jpeg" properties="cover-image" />
   <item id="cover-page" href="text/cover.xhtml" media-type="application/xhtml+xml" />
   <item id="css" href="styles/main.css" media-type="text/css" />
   ```

3. **`OEBPS/text/cover.xhtml`** lives inside `OEBPS/text/`, so it steps up one directory (`../`) to reach `styles/` and `images/`:

   ```xml
   <link rel="stylesheet" type="text/css" href="../styles/main.css" />
   <!-- ... -->
   <img src="../images/cover.jpg" alt="Book cover: Moby-Dick by Herman Melville" />
   ```

> [!WARNING]
> Never start relative paths in EPUB files with a leading slash (`/`, such as `/OEBPS/images/cover.jpg` or `/images/cover.jpg`). Leading-slash paths are invalid in OCF containers and fail across reading systems. Also remember that all paths are **case-sensitive**—`../Images/cover.jpg` will not match `OEBPS/images/cover.jpg`.

## Specifications

- [EPUB 3.3: File and directory structure](https://www.w3.org/TR/epub-33/#sec-container-file-and-dir-structure)
- [EPUB 3.3: File paths and file names](https://www.w3.org/TR/epub-33/#sec-container-filenames)
- [EPUB 3.3: URLs in the OCF abstract container](https://www.w3.org/TR/epub-33/#sec-container-iri)
- [EPUB 3.3: Parsing URLs in the package document](https://www.w3.org/TR/epub-33/#sec-parse-package-urls)

## See also

- [EPUB structure](/en-US/docs/Related/EPUB/Structure)
- [Open Container Format (OCF)](/en-US/docs/Related/EPUB/Structure/OCF)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
- [EPUB publication content](/en-US/docs/Related/EPUB/Content)
