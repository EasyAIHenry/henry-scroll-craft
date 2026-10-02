# The reverse teardown

The one generated asset on the page. One photo in, one 8-second clip out.

## Why backwards

An image-to-video model starts from the image you give it. So its first frame
matches your photo, and every frame after that is the model's invention.

- Ask it to **build** the product from empty and the last frame is a guess. It
  will be close to the real product, not the same.
- Ask it to **take the product apart** and the first frame is their real photo.
  Reverse the clip and that frame lands at the end.

The visitor sees the AI frames only while things are moving. When the page
holds still, they are looking at the shop's real photo.

## The prompt template

Fill the brackets. Keep every other word.

```
Locked-off static camera with the exact framing of the reference photo: [the
finished product, colour and model] on [the surface] against [the backdrop].
Clean product teardown in reverse build order, stop-motion style, no hands, no
people. First, [the "on" state switches off: lights, screen, steam]. Then [the
outermost part] lifts off and out of frame. [Next part] [leaves]. [Next part]
[leaves]. [...] The [bare shell] is left completely empty and still on [the
surface]. Photoreal, sharp, consistent lighting, no camera movement, no text.
```

What each part does:

| Phrase | Why it is there |
|---|---|
| `Locked-off static camera with the exact framing of the reference photo` | Stops the camera drifting, so the reversed clip ends exactly where the photo is. |
| `stop-motion style, no hands, no people` | Parts move on their own. No hands appearing in a shot of their product. |
| `First, [the on state] switches off` | Reversed, this becomes the last thing that happens: the switch-on. That is your peak. |
| `in reverse build order`, then one part per sentence | Reversed, the parts go in the order a builder would fit them. Six to nine parts fits 8 seconds. |
| `lifts off and out of frame` | Parts leave the frame cleanly instead of piling up on the table. |
| `left completely empty and still` | Gives you a clean first frame for the page to open on. |
| `no camera movement, no text` | No zoom, no captions burnt into the video. |

## Settings

- **Length:** 8 seconds.
- **Aspect ratio:** the same as the photo. A 3:4 photo gets a 3:4 clip.
- **Reference:** the photo as the start image (or the single reference image).
  One image only.
- **Resolution:** the cheapest draft first. Only pay for a higher resolution
  once the draft works.
- **Model:** any image-to-video model that takes a start image, for example
  Seedance 2.5 on Higgsfield, or Kling (untested).
- Save the result as `src/clip.mp4`. Do not edit it.

## The Ep6 prompt (worked first try)

Seedance 2.5, 8 seconds, 480p draft, one reference photo. The render took about
1 minute 20 seconds.

```
Locked-off static camera with the exact framing of the reference photo: a white
HYTE Y60 gaming PC on a wooden table against a black studio backdrop. Clean
product teardown in reverse build order, stop-motion style, no hands, no people.
First, all the blue RGB lighting switches off and the PC is lit only by soft
white studio light. Then the curved glass panels lift off and out of frame. The
white triple-fan graphics card slides out and up out of frame. The fans lift out
one by one. The white 360 liquid cooler radiator and pump lift out. The RAM
sticks pop out. The cables pull away. The motherboard lifts out. The white case
is left completely empty and still on the table. Photoreal, sharp, consistent
lighting, no camera movement, no text.
```

## Check the draft

Make a contact sheet and look at it before anything else:

```bash
ffmpeg -i src/clip.mp4 -vf "fps=4,scale=240:-2,tile=8x4" -frames:v 1 out/draft-sheet.jpg
```

Reroll if:

- The camera moved, zoomed or changed angle.
- Hands, people or text appeared.
- A part melts into another part instead of leaving.
- The first frame does not look like their photo.

Small things you can live with: a part that is not the exact model, a cable
that vanishes instead of leaving. Time a label to cover the moment, and say in
the report that the inside is close to their build, not exact.

## Reverse, hold, encode

```bash
ffmpeg -i src/clip.mp4 -vf "reverse,tpad=stop_mode=clone:stop_duration=1.5" \
  -an -c:v libx264 -crf 16 -pix_fmt yuv420p out/build.mp4
bash <skill>/scripts/encode.sh out/build.mp4 assets/build.mp4
bash <skill>/scripts/encode.sh out/build.mp4 assets/build-m.mp4 mobile
ffmpeg -i assets/build.mp4 -frames:v 1 -q:v 3 assets/poster.jpg
```

- `reverse` plays it backwards. It holds the whole clip in memory, which is fine
  for 8 seconds.
- `tpad` adds a 1.5 second hold on the last frame. That gives the scroll room to
  rest after the switch-on.
- `encode.sh` puts a keyframe every few frames so the clip seeks instantly under
  the scroll. A normal web encode plays fine and scrubs badly.
- The poster is the empty first frame, shown until the video paints.

## Three more examples (untested)

These use the same template. I have not generated them yet. Treat them as a
starting point and check the draft as above.

### A bike shop

```
Locked-off static camera with the exact framing of the reference photo: a
[matte green steel road bike] on [a workshop stand] against [a white brick
wall]. Clean product teardown in reverse build order, stop-motion style, no
hands, no people. First, the front and rear lights switch off. Then the saddle
and seat post lift out and out of frame. The handlebars lift away out of frame.
The pedals and cranks come off and leave the frame. The chain lifts away. The
front wheel and then the rear wheel roll out of frame. The brakes and gears
detach and lift away. The fork slides out. The bare frame is left completely
empty and still on [the workshop stand]. Photoreal, sharp, consistent lighting,
no camera movement, no text.
```

Peak: the lights come on as the last part goes in.

### A furniture maker (sofa)

```
Locked-off static camera with the exact framing of the reference photo: a
[three-seat sofa in rust velvet] on [a wool rug] against [a plain warm-white
wall], with [a floor lamp] beside it. Clean product teardown in reverse build
order, stop-motion style, no hands, no people. First, the floor lamp switches
off. Then the throw blanket lifts away out of frame. The scatter cushions lift
off one by one. The back cushions lift out. The seat cushions lift out. The
velvet upholstery peels away and leaves the frame. The padding lifts away. The
bare wooden frame and springs are left completely empty and still on [the
rug]. Photoreal, sharp, consistent lighting, no camera movement, no text.
```

Peak: the lamp comes on and the room warms. The empty frame at the start shows
the craft, which suits a maker.

### A café (coffee machine)

```
Locked-off static camera with the exact framing of the reference photo: a
[stainless steel two-group espresso machine] on [a marble counter] against [a
white tiled wall], with a cup of espresso under the group head. Clean product
teardown in reverse build order, stop-motion style, no hands, no people. First,
the steam stops and the warm light behind the machine switches off. Then the
cup and saucer slide out of frame. The portafilters twist out and lift away.
The drip tray slides out. The steam wands detach and lift away. The side panels
lift off and out of frame. The boiler and pipework lift out. The bare steel
chassis is left completely empty and still on [the counter]. Photoreal, sharp,
consistent lighting, no camera movement, no text.
```

Peak: the light comes on, steam rises, and the cup is there.
