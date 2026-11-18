---
title: EPUB content directory
short-title: Content directory
slug: Related/EPUB/Reference/Structure/Content
page-type: guide
sidebar: epub
---

In an unzipped EPUB, the content directory (commonly named `EPUB`, `OEBPS`, or `OPS`) is the folder that holds the [package file](/en-US/docs/Related/EPUB/Reference/Package), the [navigation document](/en-US/docs/Related/EPUB/Reference/Navigation), and all [publication content](/en-US/docs/Related/EPUB/Reference/Content).

## Naming and folder layout

Unlike [`mimetype`](/en-US/docs/Related/EPUB/Reference/Structure/mimetype) and [`META-INF`](/en-US/docs/Related/EPUB/Reference/Structure/META-INF), which have fixed names at the root of the archive, the content directory has no required name. Authoring tools usually name it `EPUB`, `OEBPS` (_Open eBook Publication Structure_), or `OPS` (_Open Publication Structure_). You can use any valid folder name as long as `full-path` on `<rootfile>` in [`META-INF/container.xml`](/en-US/docs/Related/EPUB/Reference/Structure/container.xml) matches the path to your package file.

Technically, the [Open Container Format (OCF)](/en-US/docs/Related/EPUB/Reference/Structure/OCF) allows placing the package file and content files directly in the archive root. In practice, placing the package file at the top of a dedicated content directory and keeping all book files inside that directory (or subfolders beneath it) is strongly recommended: it separates book files from container files and avoids issues in reading systems that block access to parent directories outside the package file's folder.

Subfolders inside the content directory (such as `fonts`, `images`, `styles`, and `text`) are optional. Short books sometimes keep all files flat in the content directory, while larger books group assets by type. Every publication resource stored in the content directory must be listed in the [manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest).

## Relative paths across an EPUB

A common cause of broken links and missing images in hand-edited EPUBs is using the wrong base folder for relative paths. Relative URLs resolve from one of three base locations depending on which file contains the link:

| File containing the path                                                                                                                                                                                                                       | Path is relative to                                        | Example path to `EPUB/images/cover.jpg`                              |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- | -------------------------------------------------------------------- |
| Files in [`META-INF/`](/en-US/docs/Related/EPUB/Reference/Structure/META-INF) ([`container.xml`](/en-US/docs/Related/EPUB/Reference/Structure/container.xml), [`encryption.xml`](/en-US/docs/Related/EPUB/Reference/Structure/encryption.xml)) | Archive root directory                                     | `EPUB/package.opf` (or `EPUB/fonts/display.otf` in `encryption.xml`) |
| [Package file](/en-US/docs/Related/EPUB/Reference/Package) (`EPUB/package.opf`)                                                                                                                                                                | Folder containing `package.opf` (`EPUB/`)                  | `images/cover.jpg`                                                   |
| Content files (`EPUB/text/cover.xhtml`, `EPUB/styles/main.css`)                                                                                                                                                                                | Folder containing that file (`EPUB/text/`, `EPUB/styles/`) | `../images/cover.jpg`                                                |

For example, in an archive whose content directory is `EPUB/` (see the directory layout in [EPUB structure](/en-US/docs/Related/EPUB/Reference/Structure)):

1. `META-INF/container.xml` resolves from the archive root, so it includes `EPUB/`:

   ```xml
   <rootfile full-path="EPUB/package.opf" media-type="application/oebps-package+xml" />
   ```

2. `EPUB/package.opf` is already inside `EPUB/`, so manifest paths omit `EPUB/`:

   ```xml
   <item id="cover-img" href="images/cover.jpg" media-type="image/jpeg" properties="cover-image" />
   <item id="cover-page" href="text/cover.xhtml" media-type="application/xhtml+xml" />
   <item id="css" href="styles/main.css" media-type="text/css" />
   ```

3. `EPUB/text/cover.xhtml` is inside `EPUB/text/`, so it steps up one folder (`../`) to reach `styles/` and `images/`:

   ```xml
   <link rel="stylesheet" type="text/css" href="../styles/main.css" />
   <!-- ... -->
   <img src="../images/cover.jpg" alt="Book cover: Moby-Dick by Herman Melville" role="doc-cover" />
   ```

See [Container URLs](/en-US/docs/Related/EPUB/Reference/Structure/OCF#container_urls) and [File and path naming rules](/en-US/docs/Related/EPUB/Reference/Structure/OCF#file_and_path_naming_rules) for OCF restrictions on leading slashes and case sensitivity.

## Specifications

- [EPUB 3.3: File and directory structure](https://www.w3.org/TR/epub-33/#sec-container-file-and-dir-structure)
- [EPUB 3.3: File paths and file names](https://www.w3.org/TR/epub-33/#sec-container-filenames)
- [EPUB 3.3: URLs in the OCF abstract container](https://www.w3.org/TR/epub-33/#sec-container-iri)
- [EPUB 3.3: Parsing URLs in the package document](https://www.w3.org/TR/epub-33/#sec-parse-package-urls)
