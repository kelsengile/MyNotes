[Previous](./[17]-Responsive-Images.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[19]-Embedding-Content.md)

*Media*

# Lesson 18 - Audio & Video

## 18.1 The `<audio>` Element

`<audio>` embeds a sound file with built-in playback controls.

```html
<audio controls>
  <source src="song.mp3" type="audio/mpeg">
  <source src="song.ogg" type="audio/ogg">
  Your browser doesn't support the audio element.
</audio>
```

Multiple `<source>` elements let the browser pick whichever format it supports. The text after them is a fallback shown only in browsers that don't support `<audio>` at all.

---

## 18.2 The `<video>` Element

`<video>` works the same way, with a few extra useful attributes:

```html
<video controls width="640" height="360" poster="thumbnail.jpg">
  <source src="movie.mp4" type="video/mp4">
  <source src="movie.webm" type="video/webm">
  Your browser doesn't support the video element.
</video>
```

- `controls` shows play/pause/volume controls.
- `poster` sets a thumbnail image shown before playback starts.
- `autoplay`, `loop`, and `muted` are also available — use `autoplay` sparingly and almost always paired with `muted`, since browsers block unmuted autoplay by default.

---

## 18.3 The `<track>` Element for Captions

`<track>` adds captions or subtitles to `<video>`, sourced from a WebVTT (`.vtt`) file. This is essential for accessibility — deaf and hard-of-hearing users, and anyone watching without sound, rely on captions.

```html
<video controls>
  <source src="movie.mp4" type="video/mp4">
  <track src="captions-en.vtt" kind="captions" srclang="en" label="English" default>
</video>
```

- `kind` can be `captions`, `subtitles`, `descriptions`, or `chapters`.
- `default` marks which track shows automatically if the user hasn't chosen one.

[Previous](./[17]-Responsive-Images.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[19]-Embedding-Content.md)
