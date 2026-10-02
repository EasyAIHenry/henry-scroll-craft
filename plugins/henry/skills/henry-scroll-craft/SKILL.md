---
name: henry-scroll-craft
description: >
  Henry's scroll-craft. Build a scroll-driven website for a local business that
  has great photos and no website. Take one real photo of their finished
  product, ask an image-to-video model (Seedance 2.5, Kling or similar) for a
  teardown of it, play the clip backwards under the scroll so the product builds
  itself as the visitor scrolls, tick a label for each part on the frame it goes
  in, switch it on at the end and land on their real photo. Then their real
  collection, their public facts and one WhatsApp or booking button. Optional
  Figma design step through the Figma connector. Verifies desktop, phone and
  reduced motion. Use for "henry scroll-craft", "scroll site for a shop",
  "reverse teardown", "build it backwards", "a website from one photo", "a site
  where the product builds as you scroll", "scroll website for a local
  business", "concept site for a client with no website",
  "/henry-scroll-craft".
allowed-tools: Bash, Read, Write, Edit, Glob, Grep, AskUserQuestion
---

# Henry's scroll-craft

One real photo of a business's best work becomes the whole website. You ask a
video model to take the product apart, then play that clip backwards under the
scroll. The visitor scrolls, the product builds itself part by part, it switches
on, and the last frame is the shop's real photo.

**Who it is for:** local businesses with good photos on Instagram or Google Maps
and no website. A PC builder, a bike shop, a furniture maker, a café.

**What you hand over:** `BRIEF.md`, the reversed clip encoded for scrubbing, one
real HTML page on the scroll-craft engine, screenshots from desktop, phone and
reduced motion, and a short list of what the owner still has to approve.

`<skill>` below means the folder this `SKILL.md` sits in. Claude Code prints it
as the base directory when the skill loads. It works the same whether the skill
came in as a plugin or was copied as a personal skill to
`~/.claude/skills/henry-scroll-craft/`: always resolve `scripts/`, `engine/`,
`references/` and `templates/` relative to this file, never to a fixed path.

## Before you start

```bash
node <skill>/scripts/doctor.mjs              # node 18+, a full ffmpeg, playwright, Chrome
node <skill>/scripts/workspace.mjs --ensure  # prints the workspace and creates it
```

The doctor only checks. It never installs anything. If it lists something
missing, tell the user what it is and offer to install it (on a Mac with
Homebrew: `brew install node ffmpeg`, Chrome from google.com/chrome). Wait for a
yes before installing system software. `playwright-core` is different: install
it yourself inside the build folder in Step 5.

Each site lives in `<workspace>/builds/<name>/`. Do not work around a missing
ffmpeg: a stripped build fails later with an error that looks like a typo in
your command.

Build folder layout:

```
builds/<name>/
  BRIEF.md
  src/photo.jpg      their finished-product photo (the last frame)
  src/clip.mp4       the teardown, exactly as the model gave it
  out/               working files, not shipped
  assets/            what the page loads
  index.html  site.css  site.js  scrollcraft.js  scrollcraft.css
  lab/               screenshots from the checks
```

## Step 1: The brief

Ask eight short questions in one message. The user can answer "you decide" to
any of them. When they do, write your pick next to it and mark it
`Claude's pick`. Do not ask again later.

1. **The business.** What they sell, where, and where their photos are.
2. **Vibe in three to five words.** Their own shoots are the best reference.
3. **The journey.** What the visitor sees first, next and last, in their order.
4. **Energy.** Where the page is calm and where it is loud.
5. **The one moment.** The sentence a visitor would say to a friend.
6. **What this site does that no other site does.**
7. **The hero photo.** One finished product, whole, on a plain backdrop.
8. **The close.** One action (WhatsApp, booking link, call) and which public
   facts to show (rating, review count, delivery, warranty, hours).

Write the answers into `BRIEF.md` in their words, with a feeling curve, the
peak, and the open items for the owner. Template, a filled example and how to
pick the hero photo: [references/local-business-brief.md](references/local-business-brief.md).

## Step 2: Design in Figma (optional)

If the Figma connector is connected, draw the page in Figma before writing code
(in the Claude app: Customize > Connectors > + > Figma > Connect). Upload their
photos, then draw a desktop frame (1440 wide) and a phone frame
(390 wide) with three hero states: empty, mid-build with one label, switched on.
Add the collection, the facts and the close. Mine took 5 minutes.

Then build from that frame. If there is no connector, skip this step. The brief
is enough. Details and the premium checklist: [references/figma-design.md](references/figma-design.md).

## Step 3: The reverse-teardown clip

The model starts from the photo you give it, so its first frame matches their
photo. Ask for a **teardown** and the first frame is the finished product. Play
it backwards and that frame becomes the last one. The visitor ends on the real
thing instead of the model's guess at it.

Prompt template. Fill the brackets, keep everything else:

```
Locked-off static camera with the exact framing of the reference photo: [the
finished product, colour and model] on [the surface] against [the backdrop].
Clean product teardown in reverse build order, stop-motion style, no hands, no
people. First, [the "on" state switches off: lights, screen, steam]. Then [the
outermost part] lifts off and out of frame. [Next part] [leaves]. [Next part]
[leaves]. [...] The [bare shell] is left completely empty and still on [the
surface]. Photoreal, sharp, consistent lighting, no camera movement, no text.
```

Any image-to-video model that takes a start or reference image works, for
example Seedance 2.5 on Higgsfield, or Kling (untested). Settings: **8
seconds**, the **same aspect ratio as the photo**, the cheapest draft resolution
first. The user runs the generation and pays for it. Ask before spending
anything on their behalf. Save the result as `src/clip.mp4`.

Look at the draft before you use it. Then reverse it, add a short hold on the
last frame, and encode for scrubbing:

```bash
ffmpeg -i src/clip.mp4 -vf "reverse,tpad=stop_mode=clone:stop_duration=1.5" \
  -an -c:v libx264 -crf 16 -pix_fmt yuv420p out/build.mp4
bash <skill>/scripts/encode.sh out/build.mp4 assets/build.mp4
bash <skill>/scripts/encode.sh out/build.mp4 assets/build-m.mp4 mobile
ffmpeg -i assets/build.mp4 -frames:v 1 -q:v 3 assets/poster.jpg
cp src/photo.jpg assets/photo.jpg
```

What to check in the draft, when to reroll, and three more examples (a bike, a
sofa, a coffee machine): [references/reverse-teardown.md](references/reverse-teardown.md).

## Step 4: Build the page

Copy `<skill>/engine/scrollcraft.js` and `scrollcraft.css` into the build folder.
Never edit them. Start from [references/template.html](references/template.html),
delete what you do not need, and write real HTML: real headings, real text, real
links. Your own behaviour goes in `site.js` and `site.css`.

The page, in order ([references/page-order.md](references/page-order.md) has the
feeling curve and the engine attribute for each section):

1. **The build.** A pinned `scrub` act with the reversed clip. The product
   starts empty beside the headline and builds as you scroll.
2. **Labels synced to the clip.** Each label carries the second its part goes
   in (`data-at="3.3"`). `site.js` reads the video's `currentTime` every frame
   and shows the label whose time has passed. One label at a time. On a phone,
   one line under the product.
3. **The peak.** On the last part the room dims for a beat, the lights come on,
   a soft glow blooms, and the headline lands.
4. **Their real photo.** Once the clip settles, crossfade their real photo over
   the last frame. Hold it for a moment before the next section.
5. **Their real collection.** Their other work, walked sideways, with the names
   and specs they posted.
6. **The facts.** Only public ones, as plain labels.
7. **The close.** One button: WhatsApp or booking. Same label everywhere.

Code for steps 2 to 4, and how to read the part times off a contact sheet:
[references/sync-to-clip.md](references/sync-to-clip.md).

**The look.** My first pass looked tacky. A second ask for "seamless and
premium" fixed most of it, and a later pass fixed the type and the build. Both
are now the default:

- Page colour sampled from their photo backdrop, so the product sits in the page
  with no visible box. Feather the photo edges with a mask.
- One accent colour, taken from the product itself.
- One type family: Geist for headlines and text, Geist Mono only for small
  uppercase labels. Tracking tightens as type grows, and headlines break on
  phrases. No italic serif word in a sans headline.
- The build fills the screen. Each label draws a thin line from its part, and
  never more than three labels show at once.
- Film grain over everything at about 5% opacity. It ties the AI clip and the
  real photos into one picture.
- Text beside the product, never on it.

The numbers for both passes are in [references/figma-design.md](references/figma-design.md).

Theme with the six `--sc-*` variables and two fonts (see the template). The
design floor for spacing, type and contrast is [references/taste.md](references/taste.md).
The engine's scroll devices are in [references/devices.md](references/devices.md).

## Step 5: Verify

```bash
cd <build folder> && npm i playwright-core      # once per build
node <skill>/scripts/serve.mjs --root . --port 4500 &
node <skill>/scripts/shoot.mjs --url http://localhost:4500 --out lab/shots
node <skill>/scripts/shoot.mjs --url http://localhost:4500 --out lab/mobile --width 390 --height 844
node <skill>/scripts/shoot.mjs --url http://localhost:4500 --out lab/reduced --reduced-motion
```

The harness reports dead scroll, copy that never fully appears, and contrast on
the real composited page. Then open each `sheet.png` and check by eye:

- No dead scroll anywhere. Every stretch of scroll changes something.
- Each label appears on the frame its part goes in, and is readable.
- The dim, the lights and the headline land in that order.
- The last build frame is their real photo, with no jump in framing.
- On the phone, the product is centred and the label sits under it.
- With reduced motion, the page shows their real photo and the full parts list,
  with nothing waiting on the scroll.
- The button opens the right link.

A green run does not cover a real phone. Serve with `--lan`, open it on a phone
on the same Wi-Fi, then stop the server. Full procedure and known failures:
[references/verify.md](references/verify.md). For a phone-only bug, ship
[references/device-diag.html](references/device-diag.html) beside the page.

## Step 6: Honesty rules

These are not optional. A concept site for a real business has to be honest
about what it is.

| Rule | How |
|---|---|
| Say it is a concept | Footer on every page: `Concept by <maker>. Not affiliated with <business>.` Keep it until the owner signs off. |
| Never invent numbers | No made-up stats, reviews, quotes, prices or awards. No number, no counter. |
| Use only their material | Their own photos and their public facts. Write the source and the date for each fact in `BRIEF.md`. |
| The clip is the only AI media | No AI stills of their products. The clip must end on their real photo. |
| Ask before publishing | The site stays on localhost or a private link until the owner says yes. Ask them before you post it or send the link to anyone else. |
| Business details only | Their public business contact, nothing personal. Leave the button as `#book` in anything shared before the owner agrees. |

If anyone asks, say the build clip is AI. The inside may not match their real
build part for part. The final frame is their photo.

## Never

| Never | Instead |
|---|---|
| Edit `scrollcraft.js` or `scrollcraft.css` | Your own code in `site.js` and `site.css`, driven off `--sc-p` or the clip's `currentTime` |
| Text baked into the clip | Real HTML on top |
| A scroll arrow, a mouse icon, "scroll to explore" | Nothing. The product moving is the cue |
| Em dashes in visible copy | A full stop, comma or colon |
| A dark overlay over the whole frame for contrast | Put the text beside the product |
| Sound on the clip | `encode.sh` strips it |
| More than one button at the close | One action, one label |
| Shipping without Step 5 | Run Step 5 |

## Output

Report briefly: where `BRIEF.md` is, the clip prompt you used, the part times,
what you checked on each sheet, what you could not check (a real phone, at
least), the open items for the owner, and the local URL.

## Where this comes from

This skill is adapted from Nate Herk's scroll-craft (MIT). The engine, the
scripts, `devices.md`, `verify.md`, `taste.md`, `template.html` and
`device-diag.html` are his, carried over unchanged (except `doctor.mjs`, which
no longer checks for a kie.ai key). Those files mention a few files that are not
in this plugin (`assets.md`, `feel.md`, `uniqueness.md`, `worldflight.md`,
`approved-collection.md`, `kie.mjs`). Skip those links. Everything this workflow
needs is here. The workspace also seeds `FINGERPRINTS.md` from his skill. It is
optional here: add a row per site if you want to stop your sites looking alike.
