# Content blocks

The body of a rendered document is a list of typed **content blocks**. Send them
as `v: 2` plus `blocks` in `render_branded_documents`.

A block says **what** to write. The renderer decides how to print it in PDF,
DOCX and PPTX: that is what keeps the three formats equivalent, and it is why
there are no coordinates, no page sizes, no font sizes and no custom HTML in a
block. Say the content, not the layout.

`id` is optional: the server assigns one when you leave it out. Never invent
ids, and never use the same one twice.

## Catalog

| kind | fields | use it for |
| --- | --- | --- |
| `heading` | `text`, `level` 1-3 | opening a part of the document. **Level 1 always starts a new slide.** |
| `text` | `body` | prose. Plain text, line breaks are kept, no markdown |
| `bullets` | `items` 1-20, `columns` 1 or 2 | lists. Two columns for short parallel items |
| `table` | `columns` 1-6, `rows`, `align` | comparisons, matrices, schedules. One cell per column |
| `pricing` | `pricing: { currency, tax_note?, items }` | the commercial table. **One per document** |
| `image` | `asset: { scope, path }`, `caption?` | a photo or a diagram already in the File Explorer |
| `chart` | `chart_type`, `labels` 2-12, `series` 1-3, `caption?` | numbers with a shape: trend, split, comparison |
| `kpi` | `items` 2-4 of `{ value, label }` | the two to four figures that carry the argument |
| `terms` | `items` 1-20 of `{ label, value }` | validity, payment, timing: short label plus short value |
| `callout` | `body`, `tone` `accent` or `neutral` | one sentence that must not be missed. The closing call to action |
| `columns` | `left`, `right`, 1-6 blocks each | two things read side by side, not one thing cut in half |
| `page_break` | none | forcing a page or a slide to end here |

Price lines are `{ description, quantity, unit_price, unit?, product_id? }`.

## Rules the renderer enforces

- **One `pricing` block per document**, and never `subtotal` or `total` inside
  it: the renderer recalculates them and rejects a figure that disagrees. A line
  total is quantity times unit price.
- **`columns` nests one level only** and holds leaf blocks: no `columns`,
  `pricing`, `terms` or `page_break` inside it.
- **`image.asset` is a File Explorer reference**, never a URL and never base64.
  Up to 8 distinct images per document, PNG, JPEG or WebP. An image that cannot
  be resolved becomes a warning, not a failed render: check the warnings.
- **A `chart` carries numbers, not a picture.** One value per label. It is drawn
  in the brand colors and stays editable, which a screenshot never does.
- `layout: "document"` renders PDF/DOCX, `layout: "slides"` renders PDF/PPTX.
- **Legal documents use blocks for the body and keep their `legal` metadata**
  beside it. The contract sheet, the parties table, the register of fields to
  fill, the annexes and the signature blocks are built from `legal`: writing
  them as blocks prints them twice. In a contract, stay on `heading`, `text`,
  `table` and `bullets`.

## Composing

Blocks flow. In a document they follow one another and the renderer paginates;
in slides they are packed until the slide is full, and then a new one starts.
You do not decide where a page ends, except with `page_break` and with a level-1
heading.

This changes how much you write per block. Do not compose one enormous `text`
per section: split the prose where the meaning splits, and the packing will do
the rest.

A commercial proposal in slides usually reads:

```
heading(level 1) "Proposta per <recipient>"
text             one paragraph of context
kpi              3 figures that frame the problem
heading(level 2) "Cosa comprende"
bullets          the deliverables, 1 column
chart            the trend or split that supports the offer, if there is data
heading(level 2) "Investimento"
pricing          the catalogued lines
terms            validity, payment, timing
callout(accent)  the next step, one sentence
```

A report or a case study is the same skeleton with `image` and `chart` doing the
work that `pricing` does in a proposal.

Use `columns` when two things are genuinely parallel: a before and an after, a
plan against its cost, a photo beside its explanation. Not to save space.

## What not to do

- Do not put a number in a `text` block when it is one of the two to four
  figures that carry the argument: that is a `kpi`.
- Do not describe a table in prose and then also send the table.
- Do not open a `heading` with nothing after it. A heading introduces what
  follows, and on slides it will otherwise sit alone at the bottom of one.
- Do not send an empty `bullets`, a `table` with rows shorter than its columns,
  or a `chart` with fewer values than labels: the first two fail validation, the
  third silently renders zeros.
- Do not reach for `image` to show text. Text in an image is invisible to the
  editor, to search and to whoever regenerates the document later.
