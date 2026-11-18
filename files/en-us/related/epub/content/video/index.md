---
title: EPUB video
short-title: Video
slug: Related/EPUB/Content/Video
page-type: guide
sidebar: epub
---

EPUB 3 supports video clips in [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) content documents via the HTML [`<video>`](/en-US/docs/Web/HTML/Reference/Elements/video) element. Video is mainly used in digital textbooks, technical manuals, cookbooks, and illustrated nonfiction read on tablets, phones, and computers.

## Video formats and exempt status

Unlike images, audio, and fonts, EPUB 3 defines no [core media types](/en-US/docs/Related/EPUB/Content/Core_media_types) for video because device platforms historically split between patent-encumbered and royalty-free codecs.

Instead, all video files referenced from `<video>` (and its child [`<source>`](/en-US/docs/Web/HTML/Reference/Elements/source) elements) are **exempt resources**:

- You do not need to set a `fallback` attribute on video items in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest).
- Reading systems are not required to play video; grayscale e-ink readers typically cannot play video at all.

In practice, MP4 with H.264 video and AAC audio (`video/mp4`) and WebM with VP8 or VP9 video and Vorbis or Opus audio (`video/webm`) have the widest support across tablet, desktop, and browser reading systems. You can include both formats with multiple `<source>` elements inside `<video>`.

## Embedding `<video>` in XHTML

Write boolean attributes in XML form (`controls="controls"`, `default="default"`), supply a `poster` image using an image [core media type](/en-US/docs/Related/EPUB/Content/Images) (such as `image/jpeg` or `image/png`), and include captions with [`<track>`](/en-US/docs/Web/HTML/Reference/Elements/track):

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

Declare every referenced file—the video, the `poster` image, and any `<track>` caption files—in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest):

```xml
<manifest>
  <item id="knot-video" href="video/harpoon-line-knot.mp4" media-type="video/mp4" />
  <item id="knot-poster" href="images/harpoon-knot-poster.jpg" media-type="image/jpeg" />
  <item id="knot-captions" href="video/harpoon-line-knot-en.vtt" media-type="text/vtt" />
  <!-- other manifest items -->
</manifest>
```

Timed text tracks referenced from `<track>` (such as WebVTT `text/vtt`) are also exempt resources in EPUB 3 and do not require fallbacks.

## Authoring guidelines

- Design chapters so they still make sense when video cannot play. Choose a clear `poster` frame and explain key steps in surrounding prose or a transcript. Do not rely on text placed inside `<video>...</video>` appearing on e-ink devices: most engines recognize `<video>` and show only the `poster` image when video playback is unavailable.
- Watch file size limits. High-resolution video quickly pushes `.epub` archives past retailer upload caps. Like [audio](/en-US/docs/Related/EPUB/Content/Audio) and [fonts](/en-US/docs/Related/EPUB/Content/Fonts), video may be hosted outside the container (using `https:` URLs is strongly recommended, as reading systems may block `http:`): declare the remote URL as an `<item>` in `<manifest>` and add `properties="remote-resources"` to the XHTML document's `<item>`. Keep in mind that remote videos require network access and cannot play offline.
- When bundling `.mp4` or `.webm` files inside the `.epub` ZIP archive, keep clips short and web-optimized, or store them uncompressed (`STORE`) because video streams are already compressed.

## Specifications

- [EPUB 3.3: Exempt resources — Video and Tracks](https://www.w3.org/TR/epub-33/#exempt-video)
- [EPUB 3.3: HTML `audio` and `video` fallbacks](https://www.w3.org/TR/epub-33/#sec-fallbacks-audio)
- [EPUB 3.3: Resource locations](https://www.w3.org/TR/epub-33/#sec-resource-locations)

## See also

- [`<video>`: The Video Embed element](/en-US/docs/Web/HTML/Reference/Elements/video)
- [`<track>`: The Embed Text Track element](/en-US/docs/Web/HTML/Reference/Elements/track)
- [EPUB audio and Media Overlays](/en-US/docs/Related/EPUB/Content/Audio)
- [EPUB core media types](/en-US/docs/Related/EPUB/Content/Core_media_types)
