---
title: EPUB video
short-title: Video
slug: Related/EPUB/Content/Video
page-type: guide
sidebar: epub
---

EPUB 3 allows embedding video clips inside [XHTML](/en-US/docs/Related/EPUB/Content/XHTML) content documents using the standard HTML [`<video>`](/en-US/docs/Web/HTML/Reference/Elements/video) element. Video is primarily used in digital textbooks, technical manuals, cookbooks, and enhanced nonfiction read on tablets, phones, and computers.

## Video formats and exempt-resource status

Unlike images, audio, and fonts, **EPUB 3 defines no [core media types](/en-US/docs/Related/EPUB/Content/Core_media_types) for video**, because device platforms historically split between patent-encumbered and royalty-free video codecs.

Instead, EPUB 3 classifies all video files referenced from `<video>` (and its child [`<source>`](/en-US/docs/Web/HTML/Reference/Elements/source) elements) as **exempt resources**:

- **No fallback required**: You do not need to declare a `fallback` attribute in [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest) for video files.
- **Support is not guaranteed**: Reading systems are not required to play video—for example, grayscale e-ink devices typically cannot play video at all.

In practice, **MP4 using H.264 video and AAC audio** (`media-type="video/mp4"`) and **WebM using VP8/VP9 video and Opus/Vorbis audio** (`media-type="video/webm"`) have the widest support across tablet, desktop, and browser-based reading systems. You can supply both formats using multiple `<source>` elements inside `<video>` if needed.

## Embedding `<video>` in XHTML

Use XML syntax for boolean attributes (`controls="controls"`), provide a static `poster` image (using an image [core media type](/en-US/docs/Related/EPUB/Content/Images) such as `image/jpeg` or `image/png`), and include accessible captions via [`<track>`](/en-US/docs/Web/HTML/Reference/Elements/track):

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

Declare **every** referenced asset—the video file, the `poster` image, and any `<track>` caption files—in the package [`<manifest>`](/en-US/docs/Related/EPUB/Package/Manifest):

```xml
<manifest>
  <item id="knot-video" href="video/harpoon-line-knot.mp4" media-type="video/mp4" />
  <item id="knot-poster" href="images/harpoon-knot-poster.jpg" media-type="image/jpeg" />
  <item id="knot-captions" href="video/harpoon-line-knot-en.vtt" media-type="text/vtt" />
  <!-- other manifest items -->
</manifest>
```

Note that timed text track files referenced from `<track>` (such as WebVTT `text/vtt`) are also **exempt resources** in EPUB 3 and do not require fallbacks.

## Practical authoring guidelines

- **Design for devices that cannot play video**: Because e-ink readers and print-disabled workflows may not play `<video>`, choose an informative `poster` frame and write surrounding prose or captions so the chapter still makes sense without playing the video. (Do not rely on text placed *inside* `<video>...</video>` to display on e-ink devices—many engines recognize the `<video>` tag and display only the `poster` frame even when playback is unavailable.)
- **Watch container file size vs. remote hosting**: High-resolution video quickly inflates `.epub` file sizes past retailer upload limits. Like [audio](/en-US/docs/Related/EPUB/Content/Audio) and [fonts](/en-US/docs/Related/EPUB/Content/Fonts), video is permitted to be hosted outside the `.epub` container over `https:`:
  - If you host a video remotely, you must still declare its `https:` URL as an `<item>` in `<manifest>` and add `properties="remote-resources"` to the XHTML document's `<item>` in `<manifest>`.
  - Keep in mind that remote videos cannot be played offline and may be blocked by privacy-focused reading systems that disable network access.
- **Avoid compressing video twice in the ZIP archive**: When packaging an `.epub` containing bundled `.mp4` or `.webm` files, storing already-compressed video streams uncompressed (`STORE`) or keeping clips short and web-optimized avoids slow extraction overhead on mobile devices.

## Specifications

- [EPUB 3.3: Exempt resources — Video and Tracks](https://www.w3.org/TR/epub-33/#exempt-video)
- [EPUB 3.3: HTML `audio` and `video` fallbacks](https://www.w3.org/TR/epub-33/#sec-fallbacks-audio)
- [EPUB 3.3: Resource locations](https://www.w3.org/TR/epub-33/#sec-resource-locations)

## See also

- [`<video>`: The Video Embed element](/en-US/docs/Web/HTML/Reference/Elements/video)
- [`<track>`: The Embed Text Track element](/en-US/docs/Web/HTML/Reference/Elements/track)
- [EPUB audio and Media Overlays](/en-US/docs/Related/EPUB/Content/Audio)
- [EPUB core media types](/en-US/docs/Related/EPUB/Content/Core_media_types)
