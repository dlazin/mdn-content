---
title: EPUB content directory
short-title: Content directory
slug: Related/EPUB/Structure/Content
page-type: guide
sidebar: epub
---

In an unzipped EPUB, the **content directory** (commonly named `OEBPS`, `OPS`, or `EPUB`) is the folder that holds the [package file](/en-US/docs/Related/EPUB/Package) (`.opf`), the [navigation document](/en-US/docs/Related/EPUB/Navigation) (`nav.xhtml`), and all [publication content](/en-US/docs/Related/EPUB/Content) such as XHTML chapters, CSS stylesheets, images, fonts, audio, video, and scripts.

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

## Naming and folder layout

Unlike [`mimetype`](/en-US/docs/Related/EPUB/Structure/mimetype) and [`META-INF`](/en-US/docs/Related/EPUB/Structure/META-INF), which have fixed names at the root of the archive, the content directory has no required name. Authoring tools usually name it `OEBPS` (_Open eBook Publication Structure_), `OPS` (_Open Publication Structure_), or `EPUB`. You can use any valid folder name as long as `full-path` on `<rootfile>` in [`META-INF/container.xml`](/en-US/docs/Related/EPUB/Structure/META-INF) matches the path to your `.opf` file.

Technically, the [Open Container Format (OCF)](/en-US/docs/Related/EPUB/Structure/OCF) allows placing `package.opf` and content files directly in the archive root. In practice, placing `package.opf` at the top of a dedicated content directory and keeping all book files inside that directory (or subfolders beneath it) is strongly recommended. It keeps book files separate from container files, and it avoids compatibility issues in reading systems that block access to files located in parent directories outside `package.opf`'s folder.

Subfolders inside the content directory (such as `text/`, `styles/`, `images/`, and `fonts/`) are optional. Short books sometimes keep all files flat inside `OEBPS/`, while larger books organize assets by type.

## Declaring files in the manifest

Every publication resource stored in the content directory must be listed as an `<item>` in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest). Leaving unused images, backup files, or hidden OS files such as `.DS_Store` or `Thumbs.db` in the archive without a matching `<manifest>` entry causes [EPUBCheck](https://www.w3.org/publishing/epubcheck/) errors and bookstore upload rejections.

## Relative paths across an EPUB

A common cause of broken links and missing images in hand-edited EPUBs is using the wrong base folder for relative paths. Relative URLs resolve from one of three base locations depending on which file contains the link:

| File containing the path                                                                                | Path is relative to                                          | Example path to `OEBPS/images/cover.jpg`                               |
| ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------------------------------------------- |
| Files in [`META-INF/`](/en-US/docs/Related/EPUB/Structure/META-INF) (`container.xml`, `encryption.xml`) | Archive root directory                                       | `OEBPS/package.opf` (or `OEBPS/fonts/display.otf` in `encryption.xml`) |
| [Package file](/en-US/docs/Related/EPUB/Package) (`OEBPS/package.opf`)                                  | Folder containing `package.opf` (`OEBPS/`)                   | `images/cover.jpg`                                                     |
| Content files (`OEBPS/text/cover.xhtml`, `OEBPS/styles/main.css`)                                       | Folder containing that file (`OEBPS/text/`, `OEBPS/styles/`) | `../images/cover.jpg`                                                  |

For example, using the directory layout at the start of this page:

1. `META-INF/container.xml` resolves from the archive root, so it includes `OEBPS/`:

   ```xml
   <rootfile full-path="OEBPS/package.opf" media-type="application/oebps-package+xml" />
   ```

2. `OEBPS/package.opf` is already inside `OEBPS/`, so `<manifest>` paths omit `OEBPS/`:

   ```xml
   <item id="cover-img" href="images/cover.jpg" media-type="image/jpeg" properties="cover-image" />
   <item id="cover-page" href="text/cover.xhtml" media-type="application/xhtml+xml" />
   <item id="css" href="styles/main.css" media-type="text/css" />
   ```

3. `OEBPS/text/cover.xhtml` is inside `OEBPS/text/`, so it steps up one folder (`../`) to reach `styles/` and `images/`:

   ```xml
   <link rel="stylesheet" type="text/css" href="../styles/main.css" />
   <!-- ... -->
   <img src="../images/cover.jpg" alt="Book cover: Moby-Dick by Herman Melville" role="doc-cover" />
   ```

> [!WARNING]
> Do not start relative paths with a leading slash (`/`, such as `/OEBPS/images/cover.jpg` or `/images/cover.jpg`). Leading slashes fail across EPUB reading systems. All paths are also case-sensitive: `../Images/cover.jpg` will not match `OEBPS/images/cover.jpg`.

## Specifications

- [EPUB 3.3: File and directory structure](https://www.w3.org/TR/epub-33/#sec-container-file-and-dir-structure)
- [EPUB 3.3: File paths and file names](https://www.w3.org/TR/epub-33/#sec-container-filenames)
- [EPUB 3.3: URLs in the OCF abstract container](https://www.w3.org/TR/epub-33/#sec-container-iri)
- [EPUB 3.3: Parsing URLs in the package document](https://www.w3.org/TR/epub-33/#sec-parse-package-urls)

## See also

- [EPUB structure](/en-US/docs/Related/EPUB/Structure)
- [Open Container Format](/en-US/docs/Related/EPUB/Structure/OCF)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
- [EPUB publication content](/en-US/docs/Related/EPUB/Content)
