---
title: EPUB audio and Media Overlays
short-title: Audio
slug: Related/EPUB/Content/Audio
page-type: guide
sidebar: epub
---

EPUB 3 supports audio in two distinct ways:

1. **Embedded audio clips** inside [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) content documents using the HTML [`<audio>`](/en-US/docs/Web/HTML/Reference/Elements/audio) element (for example, language pronunciations, music excerpts, interviews, or birdcalls).
2. **Media Overlays (`application/smil+xml`)**, which synchronize prerecorded human narration with text elements in XHTML content documents to provide a highlighted read-aloud experience.

## Supported audio formats

EPUB 3 defines three **audio [core media types](/en-US/docs/Related/EPUB/Content/Core_media_types)** that audio-capable reading systems support without requiring fallback formats:

| Format | `media-type` in `<manifest>` |
| --- | --- |
| MP3 audio | `audio/mpeg` |
| AAC LC audio in an MP4 container (`.m4a`, `.mp4`) | `audio/mp4` |
| Opus audio in an Ogg container (`.opus`, `.ogg`) | `audio/ogg; codecs=opus` |

MP3 (`audio/mpeg`) and AAC (`audio/mp4`) have the broadest support across mobile, desktop, and web-based EPUB reading systems.

## Embedding `<audio>` clips in XHTML

Include standalone audio clips in an XHTML content document with the [`<audio>`](/en-US/docs/Web/HTML/Reference/Elements/audio) element, using XML attribute syntax (`controls="controls"` rather than bare `controls`), and declare every audio file in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest):

```xml
<figure>
  <figcaption>Listen to the traditional sea shanty "Haul Away Joe":</figcaption>
  <audio controls="controls" src="../audio/haul-away-joe.mp3"></audio>
</figure>
```

### Foreign audio fallbacks and transcripts

- **Codec fallbacks (`<source>`)**: If you include a non-core (*foreign*) audio format such as FLAC (`audio/flac`) or WAV (`audio/wav`), you must include a child [`<source>`](/en-US/docs/Web/HTML/Reference/Elements/source) element referencing at least one audio core media type (such as `audio/mpeg`). Text placed inside `<audio>...</audio>` does **not** count as an EPUB codec fallback because modern reading systems recognize the `<audio>` element itself and never render its inner fallback text when only the codec is unsupported.
- **Accessibility and speakerless devices**: Many readers use deaf or hard-of-hearing accommodations—or read on e-ink devices that have no speakers or headphone jack. Provide visible transcripts (or a linked transcript section in the book) and/or timed captions via [`<track>`](/en-US/docs/Web/HTML/Reference/Elements/track) whenever an audio clip conveys information not already present in the surrounding text.
- **Container vs. remote storage**: Audio is one of the few resource types that EPUB 3 permits hosting outside the `.epub` container over `https:` to keep file sizes small. If an XHTML document references a remote audio URL, you must still list the remote URL in `<manifest>` and add `properties="remote-resources"` to the XHTML document's `<item>` in `<manifest>`. Bundling short audio clips inside the container is recommended whenever offline playback matters.

## Synchronized narration with Media Overlays

Whereas `<audio>` places a standalone player widget on the page, **Media Overlays** synchronize prerecorded audio narration with the text of an XHTML chapter so reading systems can highlight each sentence or phrase as it is spoken (commonly used in children's picture books, language-learning texts, and accessible read-aloud editions).

Media Overlays use a profile of **SMIL 3.0** (`application/smil+xml`) built from three core elements:

- `<body epub:textref="...">`: The root sequence for the overlay document.
- `<seq id="..." epub:textref="..." epub:type="...">`: Groups related `<par>` or nested `<seq>` blocks (such as chapters, figures, or footnotes) so reading systems know where structural sections start and end.
- `<par id="...">`: Pairs a single `<text src="..." />` fragment in the XHTML document with an `<audio src="..." clipBegin="..." clipEnd="..." />` time slice in a core audio file.

### Media Overlays example

1. Add `id` attributes to each synchronized phrase or sentence in your XHTML content document (`text/chapter-1.xhtml`):

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

3. Link the `.smil` file to its XHTML document using `media-overlay` on `<item>` in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest), and declare the overlay durations (plus an optional CSS highlight class via `media:active-class`) in [`<metadata>`](/en-US/docs/Related/EPUB/Package/Metadata):

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

Media Overlays degrade gracefully: reading systems that do not support synchronized narration simply ignore the `.smil` files and display the XHTML chapters normally.

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
