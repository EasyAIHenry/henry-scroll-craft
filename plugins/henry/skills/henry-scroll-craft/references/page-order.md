# Page order and the feeling curve

A local-business site has one job: get a stranger from "what is this" to
pressing one button. The order below did that in Ep6. Change it when the brief
says so.

## The feeling curve

Write one line per section in `BRIEF.md` before you build: the feeling, then
what on screen causes it.

| # | Feeling | What causes it | Energy |
|---|---|---|---|
| 1 | Curiosity | The empty product on its table. A label reads 01 of N. | Calm |
| 2 | Anticipation | Parts arrive under the scroll. The label ticks up. | Rising |
| 3 | Delight (the peak) | The last part goes in. The room dims, the product switches on, the page takes its light. Their real photo settles in. | Loudest |
| 4 | Pause | A short hold on the finished product. Nothing new arrives. | Still |
| 5 | Trust | Their real collection, walked sideways, with real names and specs. | Calm |
| 6 | Confidence | The public facts as plain labels: rating, delivery, warranty, hours. | Steady |
| 7 | Resolve | One line, one button. | Firm |

Two neighbouring sections with the same feeling means one is filler. Cut it.

## The peak

The peak is the switch-on. It gets the most scroll room, the only generated
clip, and a quiet beat before it (the dim). Write it as the sentence a visitor
would say to a friend:

> "I scrolled and the PC built itself, and when the last part went in the whole
> page lit up blue."

Every product has an "on" state you can use:

| Product | The switch-on |
|---|---|
| PC | The lights come on |
| Bike | The front and rear lights come on |
| Sofa | The floor lamp comes on and the room warms |
| Coffee machine | The warm light comes on, steam rises, the cup is there |
| Cake | The candles light |
| Guitar | The amp light comes on |

## Sections and engine attributes

| # | Section | Engine device | Notes |
|---|---|---|---|
| 1 to 4 | The build | `data-sc-act="scrub"` with `data-sc-clip-map="travel"`, `data-sc-dwell="0"`, span about half a viewport per part (Ep6: 4.2 for eight parts) | Labels follow the clip's `currentTime`. See [sync-to-clip.md](sync-to-clip.md). |
| 5a | A short statement | `data-sc-act="pin"`, span about 1.7 | One or two sentences of public facts. Words light up as you scroll (drive their opacity off `--sc-p`). |
| 5b | The collection | `data-sc-act="pan"` (Ep6: span 5.4 for seven builds) | Their own photos with their own captions. |
| 6 | The facts | `data-sc-act="flow"` with `data-sc-in` | Use `data-sc-count` only for real public numbers. |
| 7 | The close | `data-sc-act="flow"` | One button, the concept footer inside the section so the page ends on it. |

`data-sc-clip-map="travel"` keeps the whole build inside the pinned stretch,
so the labels sit still while the parts go in. The build is the first thing on
the page, so there is no slide-in freeze. The hold at the end of the clip is
the pause in row 4, and it is meant to sit still. Read "Clip time is not cue
time" in [devices.md](devices.md) before using it anywhere else on a page.

## Navigation

Keep it to the business name and the same button label as the close ("Book a
visit"). On a phone, show only the button.

## Ep6 scroll, for timing

On desktop the whole page took 56 seconds to scroll at reading pace:

| Time | What is on screen |
|---|---|
| 0:00 to 0:02 | Headline beside the empty case |
| 0:02 to 0:13 | The build, one label per part |
| about 0:13 | Dim, lights on, "Switched on." |
| 0:15 to 0:18 | Hold on the finished PC |
| 0:18 to 0:27 | The statement lights up word by word |
| 0:29 to 0:41 | Seven more real builds slide past |
| 0:43 to 0:46 | The facts |
| 0:48 to 0:56 | The close and the button |
