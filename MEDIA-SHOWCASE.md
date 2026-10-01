---
title: Media Showcase
coverImage: https://raw.githubusercontent.com/stephensmitchell/docsTest/main/clipboard-image-1.jpg
tags: [media, reference, examples]
categories: [reference]
excerpt: Images, video, iframes, audio, captions, and gallery layouts in The Tool Store's blog renderer.
---

# Media Showcase

This post shows each media pattern The Tool Store renders. Copy any of
them into your own posts.

## 1. Cover image (frontmatter)

The reader shows this file's `coverImage` as a hero image above the
title, and medium and large cards in the post list show it too. Use an
absolute URL. Relative paths only resolve for the local manifest.

The cover for this post is `clipboard-image-1.jpg`. It lives in this
GitHub repo and loads from the `raw.githubusercontent.com` CDN.

## 2. Images bundled in this source

Point markdown image syntax at the raw GitHub URL of a file checked in
next to this post. It renders like any other markdown image, with no
extra config.

![clipboard-image-2.jpg](https://raw.githubusercontent.com/stephensmitchell/docsTest/main/clipboard-image-2.jpg)

![clipboard-image-3.jpg](https://raw.githubusercontent.com/stephensmitchell/docsTest/main/clipboard-image-3.jpg)

![clipboard-image-4.jpg](https://raw.githubusercontent.com/stephensmitchell/docsTest/main/clipboard-image-4.jpg)

Raw-URL pattern for repo-hosted images:
`https://raw.githubusercontent.com/{owner}/{repo}/{branch}/{path-to-file}`

## 3. Markdown images from an external CDN

`![alt](url)` works with third-party CDNs too. The blog's
`.blog-prose img` CSS makes images responsive and adds rounded corners
and vertical spacing.

![A wide placeholder photo](https://picsum.photos/seed/showcase-1/1200/600)

Put a caption in the paragraph below the image:

> *Figure 1.* Placeholder photo at 1200×600. Source: picsum.photos.

## 3. HTML `<img>` with explicit sizing

Use raw HTML when you need attributes the markdown syntax doesn't expose
(width, height, loading, decoding hints).

<img
  src="https://picsum.photos/seed/showcase-2/800/450"
  alt="Centered image with explicit width"
  width="800"
  height="450"
  loading="lazy"
  style="display:block; margin: 1em auto;"
/>

## 4. Inline image (smaller, in-flow)

Use a regular `<img>` for logos and icons that sit in the text flow:

<img src="https://picsum.photos/seed/inline-icon/64/64" alt="Tiny icon" width="32" height="32" style="display:inline; vertical-align:middle; margin-right:6px;" />
This sentence has an inline image to its left.

## 5. Direct video file (HTML5 `<video>`)

For self-hosted MP4 / WebM video, embed a `<video>` tag with controls:

<video
  src="https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4"
  controls
  preload="metadata"
  width="100%"
  style="max-width: 720px; display: block; margin: 1em auto; border-radius: 8px;"
></video>

Tips:

- `preload="metadata"` loads enough to show the player and duration
  without downloading the whole file.
- `controls` shows the native player UI. For a background or hero
  video, remove it and add `autoplay muted loop playsinline`.
- To give browsers fallback formats, use `<source>` children:

```html
<video controls width="100%" style="max-width: 720px;">
  <source src="https://example.com/clip.webm" type="video/webm" />
  <source src="https://example.com/clip.mp4" type="video/mp4" />
  Your browser does not support HTML5 video.
</video>
```

## 6. YouTube embed (`<iframe>`)

Wrap YouTube's standard embed in an aspect-ratio container so it scales
with the page width:

<div style="position: relative; width: 100%; max-width: 720px; aspect-ratio: 16/9; margin: 1em auto;">
  <iframe
    src="https://www.youtube-nocookie.com/embed/M7lc1UVf-VE"
    title="Sample YouTube embed"
    style="position: absolute; inset: 0; width: 100%; height: 100%; border: 0; border-radius: 8px;"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
    allowfullscreen
  ></iframe>
</div>

> `youtube-nocookie.com` is YouTube's privacy-enhanced mode. It sets
> fewer cookies and holds the recommended-videos overlay until the user
> interacts.

> **Embeddability gotcha: YouTube error 153 / 150 / 101.** You can't
> embed some YouTube videos. The uploader may have turned embedding off,
> or a content-ID claim or region restriction blocks it. If the player
> shows "Video player configuration error" or "Video unavailable":
>
> 1. Open `https://www.youtube.com/watch?v={VIDEO_ID}` in a browser.
>    If the video plays there but the embed errors, the uploader
>    disabled embedding.
> 2. Try `https://www.youtube.com/embed/{VIDEO_ID}`. If that shows
>    the same error code, no client can embed it.
> 3. Pick a different video, or host the file yourself and use the
>    HTML5 `<video>` pattern in section 5.
>
> The example above uses `M7lc1UVf-VE`, the canonical embeddable
> sample from YouTube's IFrame API docs.

> **Electron / desktop app gotcha: error 153 on every video.**
> The packaged desktop app loads The Tool Store's renderer over
> `file://`, which gives the page no valid origin or referer. YouTube's
> player rejects embeds from that context with error 153, including
> the canonical sample above. The block comes from the transport, so
> switching videos won't fix it.
>
> Inside the desktop app, use one of these:
>
> 1. **HTML5 `<video>` with a direct MP4/WebM URL** (section 5). The
>    browser fetches the file with no third-party origin check, so it
>    plays under `file://`. Use this as the default for in-app video.
> 2. **Vimeo embed** (section 7). Vimeo's player accepts the `file://`
>    origin, and the embed plays inside the packaged Electron app.
> 3. **Open YouTube links externally.** Link to the YouTube URL with a
>    regular `<a target="_blank">` so the user's browser plays it.
>
> The YouTube iframe above works in a normal browser (web build, dev
> server), so this post keeps it for reference. Expect it to fail
> inside the packaged Electron app.

## 7. Vimeo embed

<div style="position: relative; width: 100%; max-width: 720px; aspect-ratio: 16/9; margin: 1em auto;">
  <iframe
    src="https://player.vimeo.com/video/76979871"
    title="Sample Vimeo embed"
    style="position: absolute; inset: 0; width: 100%; height: 100%; border: 0; border-radius: 8px;"
    allow="autoplay; fullscreen; picture-in-picture"
    allowfullscreen
  ></iframe>
</div>

## 8. Audio

Embed direct audio files with `<audio controls>`:

<audio
  src="https://upload.wikimedia.org/wikipedia/en/4/4a/Commodore_64_theme.ogg"
  controls
  preload="metadata"
  style="display: block; margin: 1em 0;"
></audio>

## 9. SVG

Use SVG inline or through `<img src="...svg">`. Inline SVG lets you style
and animate it with CSS:

<svg viewBox="0 0 240 80" width="240" height="80" xmlns="http://www.w3.org/2000/svg" style="display:block; margin: 1em auto;">
  <rect x="0" y="0" width="240" height="80" rx="8" fill="#0f766e" />
  <text x="120" y="50" text-anchor="middle" fill="white" font-family="system-ui" font-size="20" font-weight="600">Inline SVG</text>
</svg>

## 10. Image gallery / multi-column

Wrap the images in a `<div>` with a CSS grid. The blog's prose CSS
leaves custom HTML layout alone.

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); gap: 0.5rem; margin: 1em 0;">
  <img src="https://picsum.photos/seed/g1/400/300" alt="Gallery 1" style="width:100%; height:auto; border-radius: 6px; margin:0;" />
  <img src="https://picsum.photos/seed/g2/400/300" alt="Gallery 2" style="width:100%; height:auto; border-radius: 6px; margin:0;" />
  <img src="https://picsum.photos/seed/g3/400/300" alt="Gallery 3" style="width:100%; height:auto; border-radius: 6px; margin:0;" />
  <img src="https://picsum.photos/seed/g4/400/300" alt="Gallery 4" style="width:100%; height:auto; border-radius: 6px; margin:0;" />
</div>

## 11. Animated GIF

Embed a GIF like any other image:

<img src="https://media.giphy.com/media/3o7TKr3nzbh5WgCFxe/giphy.gif" alt="Animated placeholder" style="display:block; margin: 1em auto; max-width: 360px; border-radius: 6px;" />

## 12. Figure with caption

Use `<figure>` + `<figcaption>` for an inline caption tied to the image:

<figure style="margin: 1em 0; text-align: center;">
  <img src="https://picsum.photos/seed/figure/800/450" alt="A landscape with a caption" style="width: 100%; max-width: 720px; height: auto; border-radius: 8px; margin: 0;" />
  <figcaption style="font-size: 0.85em; color: var(--muted-foreground); margin-top: 0.4em;">
    Figure 12. A landscape photo with an HTML &lt;figcaption&gt; caption.
  </figcaption>
</figure>

## Not supported yet

- **Wiki-style `[[image-name]]` shorthand**: use standard markdown or HTML.
- **CommonMark image-with-title `![alt](url "title")`**: the title shows
  on hover and nowhere else on the page.
- **Server-side image optimization**: images load from the source URL
  as-is. For large images, host them on a CDN that supports resize
  query params.

## Security note

The renderer passes raw HTML through, so **add only repos and Gists you
trust as blog sources**. A hostile remote markdown file could ship
`<script>` tags. CMS-authored posts use the same renderer and carry the
same risk. That's acceptable for content you manage yourself. Flag it
before you open the blog to user-submitted sources.
