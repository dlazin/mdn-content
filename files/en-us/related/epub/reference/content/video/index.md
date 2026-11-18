---
title: EPUB video
short-title: Video
slug: Related/EPUB/Reference/Content/Video
page-type: guide
sidebar: epub
---

EPUB 3 supports video clips in [XHTML](/en-US/docs/Related/EPUB/Reference/Content/XHTML) content documents via the HTML [`<video>`](/en-US/docs/Web/HTML/Reference/Elements/video) element. Video is mainly used in digital textbooks, technical manuals, cookbooks, and illustrated nonfiction read on tablets, phones, and computers.

## Video formats

Unlike images, audio, and fonts, EPUB 3 defines no core media types for video; instead, video files referenced by `<video>` (and `<source>`) and caption tracks referenced by `<track>` (such as WebVTT `text/vtt`) are [exempt resources](/en-US/docs/Related/EPUB/Reference/Content/Core_media_types#exempt_resources) that do not require manifest `fallback` attributes, though reading systems are not required to play them.

In practice, MP4 with H.264 video and AAC audio (`video/mp4`) and WebM with VP8 or VP9 video and Vorbis or Opus audio (`video/webm`) have the widest support across tablet, desktop, and browser reading systems.

## Embedding video in XHTML

Write boolean attributes in XML form (`controls="controls"`, `default="default"`), supply a `poster` image using an image [core media type](/en-US/docs/Related/EPUB/Reference/Content/Images) (such as `image/jpeg` or `image/png`), and include captions with [`<track>`](/en-US/docs/Web/HTML/Reference/Elements/track):

```xml
<figure>
  <video controls="controls"
         poster="../images/harpoon-knot-poster.jpg"
         width="640"
         height="360">
    <source src="../video/harpoon-line-knot.mp4" type="video/mp4" />
    <track kind="captions"
           src="../video/harpoon-line-knot-en.vtt"
           srclang="en"
           label="English captions"
           default="default" />
  </video>
  <figcaption>Video demonstration: Tying a whaleman's bowline knot (0:45).</figcaption>
</figure>
```

Declare every referenced file—the video, the `poster` image, and any `<track>` caption files—in the [manifest](/en-US/docs/Related/EPUB/Reference/Package/Manifest):

```xml
<manifest>
  <item id="knot-video" href="video/harpoon-line-knot.mp4" media-type="video/mp4" />
  <item id="knot-poster" href="images/harpoon-knot-poster.jpg" media-type="image/jpeg" />
  <item id="knot-captions" href="video/harpoon-line-knot-en.vtt" media-type="text/vtt" />
  <!-- other manifest items -->
</manifest>
```

## Authoring guidelines

- Design chapters so they still make sense on grayscale e-ink devices that cannot play video. Choose a clear `poster` frame and explain key steps in surrounding prose or a transcript. Do not rely on text placed inside `<video>...</video>` appearing on e-ink devices: most engines recognize `<video>` and show only the `poster` image when video playback is unavailable.
- Because high-resolution video can push EPUBs past retailer size limits, EPUB 3 allows hosting video outside the container when flagged with [`properties="remote-resources"`](/en-US/docs/Related/EPUB/Reference/Package/Manifest#manifest_item_properties) in the manifest (though remote videos cannot play offline).
- When bundling MP4 or WebM files inside the EPUB archive, keep clips short and web-optimized, or store them uncompressed (`STORE`) because video streams are already compressed.

## Specifications

- [EPUB 3.3: Exempt resources — Video and Tracks](https://www.w3.org/TR/epub-33/#exempt-video)
- [EPUB 3.3: HTML `audio` and `video` fallbacks](https://www.w3.org/TR/epub-33/#sec-fallbacks-audio)
- [EPUB 3.3: Resource locations](https://www.w3.org/TR/epub-33/#sec-resource-locations)

## See also

- [`<video>`: The Video Embed element](/en-US/docs/Web/HTML/Reference/Elements/video)
- [`<track>`: The Embed Text Track element](/en-US/docs/Web/HTML/Reference/Elements/track)
- [EPUB audio and Media Overlays](/en-US/docs/Related/EPUB/Reference/Content/Audio)
- [EPUB core media types](/en-US/docs/Related/EPUB/Reference/Content/Core_media_types)
