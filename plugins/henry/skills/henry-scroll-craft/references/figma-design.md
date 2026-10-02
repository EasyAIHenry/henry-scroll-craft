# Design in Figma first (optional)

You can skip this step. The brief is enough to build from. Use Figma when you
want to see the look before any code, or show the owner a still before you show
them a site.

In Ep6, Claude drew the whole page in Figma itself through the Figma connector,
from the shop's own photos. It took 5 minutes. The site was then built from the
same design in about 10 minutes.

## Connect Figma

- **Claude app:** Customize > Connectors > + > Figma > Connect.
- **Claude Code in the terminal:** add the Figma MCP server (`/mcp` shows what is
  connected).

Check it works by asking Claude to list your Figma files or create an empty one.

## The ask

```
Use the Figma connector to draw the page for [business] in a new Figma file.
Upload these photos: [hero photo], [4 to 8 more of their products].
Look: [vibe from the brief]. Page colour sampled from the hero photo backdrop,
one accent colour from the product, Inter Tight headings with one italic serif
word, Inter for text.
Frames:
1. Desktop 1440 x 900: the hero, three states side by side. Empty with the
   headline. Mid-build with one part label. Switched on with the closing line.
2. Desktop: their collection as a sideways row, the facts, the close with one
   [WhatsApp / booking] button and the concept footer.
3. Phone 390 x 844: the same hero states, label under the product.
Only real names, specs and public facts. No invented numbers.
```

## What to look for in the frames

- The product sits in the page with no visible box around the photo.
- Text sits beside the product, never on top of it.
- One accent colour, used sparingly.
- The phone frames are designed for the phone, not shrunk from desktop.
- The footer says `Concept by <maker>. Not affiliated with <business>.`

## Build from the frame

```
Build the site from the Figma frame "[frame name]" with /henry-scroll-craft.
```

Some Figma plans cap how many times Claude can read a file each month. Read the
frame once, save a screenshot into the build folder (`lab/figma.png`), and build
and compare against that.

## When the first pass looks cheap

Mine did. The first build looked "tacky". One more ask fixed it:

```
Make it seamless and premium. The product should sit in the page like it is in
their showroom. One accent colour. Fewer, bigger words. Grain over everything.
```

What changed between the two passes:

| First pass | Premium pass |
|---|---|
| Photo in a box on a different dark grey | Page colour sampled from the photo backdrop, edges feathered with a mask |
| Several colours | One accent, taken from the product's own light |
| All labels on screen at once | One label at a time, with a thin line to the part |
| A plain sans serif | Inter Tight, one italic serif word per headline |
| Clean digital look | Film grain at about 5%, which ties the AI clip and the real photos together |
| Hard cut to the next section | A short hold after the switch-on, then the next section |

If the user calls a pass cheap, ask what they are comparing it to, then make
the changes in this table before trying anything else.

## When the premium pass still looks off

Ep6 needed a second pass after the timed test. The viewer said the fonts and
the kerning looked bad and the build did not land. This fixed both:

| Premium pass | Second pass |
|---|---|
| Inter Tight plus an italic serif word | One family. Geist 600 for headlines, 400 or 500 for text. Geist Mono only for small uppercase labels, 11 to 13 px with 0.14em tracking |
| Default tracking | Tracking tightens as type grows: about -0.045em at 88 to 120 px, -0.035em at 56 to 64 px, -0.02em at 28 to 40 px, 0 for body text at 17 px. Kerning on |
| Lines break wherever | `text-wrap: balance` on headlines, phrase breaks, never one word alone on a line. Body text at most 60 characters a line |
| The clip in a column | The clip fills the screen height, soft edges, a soft light from above, a deep vignette |
| One label at a time | A 1 px line draws from the part to its label: type in mono over the name. Newest bright, the two before it dimmed, never more than three. On a phone the labels stack under the product, no lines |
| A plain switch-on | A short burst from the product, one light band across the screen, the page shifts to the product's light colour, then a small "Level complete" over a big two-word line |

Write the time the second pass took separately from the timed build. Do not
fold it into the headline number.
