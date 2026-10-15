---
name: dynamic-content-comps
description: Place cards or listings for content a QikCMS site already holds. Use when building a section that shows products, blog posts, events, directory listings, child pages, galleries, collection items or custom content, or whenever you are about to hand-build a grid of cards.
---

# Dynamic comps, not hand-built cards

When a section stands for content the CMS already holds, it is placed as a **dynamic comp**, never
hand-built out of card comps. This is the rule most easily broken by accident, because a
hand-built grid looks right the day it ships and then quietly rots.

A dynamic comp keeps itself in step as the owner adds content. A hand-built grid does not: the
next product, post or event simply never appears, and the owner has no idea why. They did not ask
for a snapshot, they asked for a section that shows their products.

## The ones that exist

Each of these has its own dynamic comp:

- **child pages**, source `data.page_children`
- **products, events and directory listings**, source `ecommerce.products`
- **blog posts**, source `blogs.blogs`
- **galleries**
- **collection items**, where the collections feature is on
- **custom content**

Confirm the exact source from `list-component-types` rather than typing one from this list. These
are what to look for, not a substitute for reading the library.

## When a hand-built card is correct

Two cases, and say which one applies when you do it:

1. The content is **not in the CMS**. A card about a partner organization that exists nowhere as
   a record is just a card.
2. The dynamic comp **cannot do the layout** asked for, after you have checked. Call
   `get-component-type` on the dynamic comp and read its `styling_controls` before concluding
   that, because the controls cover more than people expect.

Outside those two, reach for `sections.card` and you are building something that will go stale.

## Before you build the grid

Ask yourself what the section is *of*. If the answer is a kind of thing the owner adds to over
time, it wants a dynamic comp. If the user has handed you the content as text, check whether that
content should exist as records first: it is a better outcome to say "these look like products,
shall I check whether they are in the catalog" than to hard-code eight cards.

When you do place one, tell the user it will pick up new items on its own. That is the property
they are getting, and it is worth one sentence.
