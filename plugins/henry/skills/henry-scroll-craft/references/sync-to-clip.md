# Sync labels to the clip

The part labels follow the video, not the scroll position. Each label carries
the second its part goes in. Every frame, `site.js` reads the video's
`currentTime` and shows the label whose time has passed. So a label always
appears on the frame its part lands, however fast or slow the visitor scrolls.

The engine (`scrollcraft.js`) stays untouched. Everything below is your own
`index.html`, `site.css` and `site.js`.

## 1. Find the times

Make a contact sheet of the reversed clip, four frames a second:

```bash
ffmpeg -i out/build.mp4 -vf "fps=4,scale=240:-2,tile=8x5" -frames:v 1 lab/timing.jpg
```

Tiles read left to right, top to bottom. Tile `n` (counting from 0) is at
`n x 0.25` seconds. Find the tile where each part has just seated and write the
time down. Then pick three more times:

- **Dim:** a beat before the last part seats.
- **Lights on:** the frame where the lights come on.
- **Photo:** about half a second after the lights, once the clip has settled.

Ep6, for scale (an 8-second clip plus a 1.6-second hold):

| Part | Time (s) |
|---|---|
| Case | 0 |
| Motherboard | 0.9 |
| Processor | 1.05 |
| Memory | 1.8 |
| Cooling | 3.3 |
| Fans | 4.6 |
| Graphics | 6.0 |
| Power | 6.8 |
| Dim | 6.95 |
| Lights on | 7.25 |
| Real photo | 7.7 |

## 2. The markup

```html
<div class="dim" aria-hidden="true"></div>

<section class="build" data-sc-act="scrub" data-sc-span="4.2" data-sc-dwell="0"
         data-sc-clip-map="travel" aria-label="[Product], built as you scroll">
  <div data-sc-stage class="build__stage">
    <div class="bloom" aria-hidden="true"></div>

    <figure class="hero">
      <img class="sc-stage__poster" src="assets/poster.jpg" alt="">
      <video data-sc-scrub data-sc-src="assets/build.mp4"
             data-sc-src-mobile="assets/build-m.mp4" muted playsinline aria-hidden="true"></video>
      <img class="hero__photo" src="assets/photo.jpg" alt="[Describe their real photo]">
    </figure>

    <ol class="parts" aria-label="Parts, in the order they go in">
      <li class="part" data-at="0"><span class="part__k">Case</span> <span class="part__v">[model]</span></li>
      <li class="part" data-at="0.9"><span class="part__k">Motherboard</span> <span class="part__v">[model]</span></li>
      <!-- one <li> per part, in the order they go in -->
    </ol>

    <div class="outro">
      <h2>Powered on.</h2>
    </div>
  </div>
</section>
```

- `data-sc-src`, not `src`. The engine fetches the clip itself and skips it
  under reduced motion.
- The poster is the empty first frame. It shows until the video paints.
- `hero__photo` is their real photo, stacked on top of the video and hidden
  until the end.

## 3. The script

```js
// site.js: labels follow the clip, then the switch-on and the real photo.
(() => {
  const root = document.documentElement;
  const reduce = matchMedia('(prefers-reduced-motion: reduce)').matches;
  const video = document.querySelector('.build video[data-sc-scrub]');
  const parts = [...document.querySelectorAll('.part')];
  const DIM_AT = 6.95, LIT_AT = 7.25, PHOTO_AT = 7.7;   // seconds into the reversed clip
  let shown = -2, state = '';

  function update(t) {
    let i = -1;
    parts.forEach((el, n) => { if (t >= parseFloat(el.dataset.at)) i = n; });
    if (i !== shown) {
      parts.forEach((el, n) => el.classList.toggle('is-on', n === i));
      shown = i;
    }
    const s = t >= PHOTO_AT ? 'photo' : t >= LIT_AT ? 'lit' : t >= DIM_AT ? 'dim' : '';
    if (s !== state) {
      root.classList.toggle('is-dim', s === 'dim');
      root.classList.toggle('is-lit', s === 'lit' || s === 'photo');
      root.classList.toggle('show-photo', s === 'photo');
      state = s;
    }
  }

  // Reduced motion: no scrubbing. Show the finished product and the whole list.
  if (reduce || !video) {
    root.classList.add('is-lit', 'show-photo', 'list-all');
    return;
  }
  (function loop() {
    update(video.currentTime || 0);
    requestAnimationFrame(loop);
  })();
})();
```

Load it after the engine:

```html
<script src="scrollcraft.js"></script>
<script>ScrollCraft.mount(document.body);</script>
<script src="site.js"></script>
```

## 4. The styles

```css
/* one label at a time */
.part { opacity: 0; transition: opacity .45s ease; }
.part.is-on { opacity: 1; }
html.is-lit .part { opacity: 0; }

/* the room dims a beat before the lights */
.dim { position: fixed; inset: 0; z-index: 40; pointer-events: none;
       background: #000; opacity: 0; transition: opacity .45s ease; }
html.is-dim .dim { opacity: .55; }
html.is-lit .dim { opacity: 0; transition: opacity 1.2s ease; }

/* the lights come on with a short flicker */
.bloom { position: absolute; inset: 0; opacity: 0; pointer-events: none;
         background: radial-gradient(40% 45% at 50% 48%, rgba(120,150,255,.3), transparent 70%); }
html.is-lit .bloom { animation: boot 1.1s ease forwards; }
@keyframes boot { 0%{opacity:0} 18%{opacity:.85} 30%{opacity:.25} 46%{opacity:.95} 100%{opacity:1} }

/* their real photo settles over the last frame */
.hero { position: relative; }
.hero__photo { position: absolute; inset: 0; width: 100%; height: 100%;
               object-fit: cover; opacity: 0; transition: opacity .9s ease; }
html.show-photo .hero__photo { opacity: 1; }

/* the closing line arrives after the lights */
.outro { opacity: 0; transition: opacity .9s ease .25s; }
html.is-lit .outro { opacity: 1; }

/* reduced motion: the whole list, plainly. Keep this last. */
html.list-all .part { opacity: 1; }

@media (prefers-reduced-motion: reduce) {
  .part, .dim, .hero__photo, .outro { transition: none; }
  html.is-lit .bloom { animation: none; opacity: 1; }
}
```

Tint the bloom with the product's own light colour.

## On a phone

Callouts with lines to each part do not fit beside a product on a 390-pixel
screen. Hide them and show one line under the product instead, updated with the
same times:

```html
<p class="now" aria-live="polite"><span class="now__k">Case</span> <span class="now__v">[model]</span></p>
```

In `update()`, copy the shown label's two spans into `.now__k` and `.now__v`.
Show `.now` only under your phone breakpoint.

## Things that went wrong, so you can skip them

- **The photo and the last frame do not line up.** The crossfade shows a jump.
  Crop the photo to the clip's aspect ratio, or nudge it with
  `object-position`.
- **Labels flash past on the first scroll.** If the product glides into place
  before the build starts, hold the labels until the glide has finished. In
  Ep6 they waited until the act's progress passed one ninth.
- **The label is late by a part.** You picked the frame where the part starts
  moving. Use the frame where it has seated.
- **The labels never change in a preview pane.** A hidden tab or a hidden
  preview pane pauses `requestAnimationFrame`, so the loop stops. Check with
  `shoot.mjs` (it worked for Ep6) or in a normal, visible Chrome window.
