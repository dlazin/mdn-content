---
title: EPUB audio and Media Overlays
short-title: Audio
slug: Related/EPUB/Content/Audio
page-type: guide
sidebar: epub
---

EPUB 3 supports audio in two ways:

- Standalone audio clips embedded in [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) content documents with the HTML [`<audio>`](/en-US/docs/Web/HTML/Reference/Elements/audio) element (such as pronunciations, music excerpts, or interviews).
- **Media Overlays** (`application/smil+xml`), which synchronize prerecorded human narration with XHTML text elements for a highlighted read-aloud experience.

## Supported audio formats

EPUB 3 defines three audio [core media types](/en-US/docs/Related/EPUB/Content/Core_media_types):

| Format                                               | `media-type` in `<manifest>` |
| ---------------------------------------------------- | ---------------------------- |
| MP3 audio                                            | `audio/mpeg`                 |
| AAC LC audio in an MP4 container (`.m4a`, `.mp4`)    | `audio/mp4`                  |
| Opus audio in an Ogg container (`.opus`, `.ogg`)     | `audio/ogg; codecs=opus`     |

MP3 (`audio/mpeg`) and AAC (`audio/mp4`) have the widest support across mobile, desktop, and web reading systems. Non-core audio formats such as FLAC require a child `<source>` fallback to a core audio format (see [HTML intrinsic fallbacks](/en-US/docs/Related/EPUB/Content/Core_media_types#html_intrinsic_fallbacks_recommended)).

## Embedding `<audio>` clips in XHTML

Include audio clips in an XHTML content document with [`<audio>`](/en-US/docs/Web/HTML/Reference/Elements/audio), using XML attribute syntax (`controls="controls"` rather than bare `controls`), and declare every audio file in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest):

```xml
<figure>
  <figcaption>Listen to the traditional sea shanty "Haul Away Joe":</figcaption>
  <audio controls="controls" src="../audio/haul-away-joe.mp3"></audio>
</figure>
```

- Provide a visible text transcript (or a link to a transcript section in the book) whenever an audio clip conveys information not already in the surrounding text. Many readers are deaf or hard of hearing or use e-ink devices without speakers, and `<audio>` elements do not visually render WebVTT `<track>` captions without custom scripting (or switching to [`<video>`](/en-US/docs/Related/EPUB/Content/Video)). Never place transcripts inside `<audio>...</audio>`, where readers cannot see them.
- Audio files may be hosted outside the `.epub` container when flagged with [`properties="remote-resources"`](/en-US/docs/Related/EPUB/Package/Manifest#manifest_item_properties) in `<manifest>`, though bundling short clips inside the container is recommended whenever offline playback matters.

## Synchronized narration with Media Overlays

Whereas `<audio>` places an inline player on the page, Media Overlays synchronize prerecorded narration with XHTML text so reading systems can highlight each sentence or phrase as it plays (common in children's books, language-learning texts, and accessible read-aloud editions).

Media Overlays use a profile of SMIL 3.0 (`application/smil+xml`) built from three main elements:

- `<body>`
  - : The root sequence for the overlay document (optionally taking `epub:textref` and `epub:type`).
- `<seq>`
  - : Groups related `<par>` or nested `<seq>` blocks (such as chapters, figures, or footnotes) using `id`, `epub:textref`, and `epub:type` so reading systems know where structural sections start and end.
- `<par>`
  - : Pairs a `<text src="..." />` fragment in the XHTML document with an `<audio src="..." clipBegin="..." clipEnd="..." />` timestamp range in an audio core media type file.

### Media Overlays example

1. Add `id` attributes to each synchronized phrase or sentence in `text/chapter-1.xhtml`:

   ```xml
   <section id="ch1" epub:type="bodymatter chapter" role="doc-chapter">
     <h1 id="s1">Chapter 1: Loomings</h1>
     <p>
       <span id="s2">Call me Ishmael.</span>
       <span id="s3">Some years ago—never mind how long precisely—having little or no money in my purse, I thought I would sail about a little.</span>
     </p>
   </section>
   ```

2. Create a SMIL Media Overlay file (`overlays/chapter-1.smil`) mapping each fragment ID to timestamps in an audio file:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <smil xmlns="http://www.w3.org/ns/SMIL"
         xmlns:epub="http://www.idpf.org/2007/ops"
         version="3.0">
     <body>
       <seq id="seq1" epub:textref="../text/chapter-1.xhtml#ch1" epub:type="bodymatter chapter">
         <par id="p1">
           <text src="../text/chapter-1.xhtml#s1" />
           <audio src="../audio/chapter-1.mp3" clipBegin="0.0s" clipEnd="2.4s" />
         </par>
         <par id="p2">
           <text src="../text/chapter-1.xhtml#s2" />
           <audio src="../audio/chapter-1.mp3" clipBegin="2.4s" clipEnd="4.1s" />
         </par>
         <par id="p3">
           <text src="../text/chapter-1.xhtml#s3" />
           <audio src="../audio/chapter-1.mp3" clipBegin="4.1s" clipEnd="11.8s" />
         </par>
       </seq>
     </body>
   </smil>
   ```

3. Link the `.smil` file to its XHTML document with `media-overlay` in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest). In [`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata), declare `media:duration` both for each `.smil` item (using `refines`) and for the book as a whole. You can also set `media:active-class` to a CSS class name defined in your chapter stylesheet (for example, `.-epub-media-overlay-active`) to style the highlighted phrase during playback:

   ```xml
   <metadata xmlns:dc="http://purl.org/dc/elements/1.1/">
     <!-- required publication metadata -->
     <meta property="media:duration" refines="#ch1-overlay">0:00:11.8</meta>
     <meta property="media:duration">0:00:11.8</meta>
     <meta property="media:active-class">-epub-media-overlay-active</meta>
   </metadata>
   <manifest>
     <item id="ch1"
           href="text/chapter-1.xhtml"
           media-type="application/xhtml+xml"
           media-overlay="ch1-overlay" />
     <item id="ch1-overlay"
           href="overlays/chapter-1.smil"
           media-type="application/smil+xml" />
     <item id="ch1-audio"
           href="audio/chapter-1.mp3"
           media-type="audio/mpeg" />
     <!-- other manifest items -->
   </manifest>
   ```

Reading systems that do not support Media Overlays ignore the `.smil` files and display the XHTML chapters normally.

## Specifications

- [EPUB 3.3: Core media types — Audio](https://www.w3.org/TR/epub-33/#cmt-grp-audio)
- [EPUB 3.3: HTML `audio` and `video` fallbacks](https://www.w3.org/TR/epub-33/#sec-fallbacks-audio)
- [EPUB 3.3: Resource locations](https://www.w3.org/TR/epub-33/#sec-resource-locations)
- [EPUB 3.3: Media Overlays](https://www.w3.org/TR/epub-33/#sec-media-overlays)

## See also

- [`<audio>`: The Embed Audio element](/en-US/docs/Web/HTML/Reference/Elements/audio)
- [EPUB video](/en-US/docs/Related/EPUB/Content/Video)
- [EPUB core media types](/en-US/docs/Related/EPUB/Content/Core_media_types)
- [EPUB package manifest](/en-US/docs/Related/EPUB/Package/Manifest)
