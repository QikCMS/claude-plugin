---
name: styling-and-tailwind
description: Style a QikCMS component or change a site's look. Use when setting colors, spacing, borders, shadows or layout classes on QikCMS comps, when matching a design or mock, or when a styling value was refused or came back changed from what was sent.
---

# Styling in QikCMS

Styling is expressed as **Tailwind 3** classes in a comp's styling keys. Not Tailwind 4, and not
CSS. The constraints below are mechanical properties of this CMS, not preferences, and several are
enforced by the API rather than trusted to the prompt.

## Start from the site, not from a palette

Call `get-site-conventions` before writing any color, font or spacing. It carries that site's
colors, fonts, global variables and content tokens. A palette you chose will look wrong next to
the rest of the site, and a second site does not inherit the first one's conventions.

Anything you create already inherits the site's global styling by default, the same as a comp
added in the builder. So **do not set styling values unless the user asked for something
specific.** `apply-stack` and `apply-blueprint` both take `global_styling`, and passing it false
only makes sense when you have been asked to keep a stack's own styling exactly.

## What the API refuses

Both of these come back as an error and nothing is written:

- **Arbitrary values.** `w-[327px]` and friends are classes this site cannot compile.
- **Grid breakpoint maps.** A grid wants a string; a per-breakpoint map produces no class at all.

## What the API silently changes

These succeed, but not with the value you sent, so check the response rather than assuming:

- **Per-breakpoint padding or margin is reduced to its mobile value.** Per-breakpoint spacing is
  not a feature here, and the owner's own controls could not show it.
- **A Tailwind 4 opacity modifier is converted to the Tailwind 3 pair.** `bg-red-500/80` becomes
  `bg-red-500` plus `background_opacity: bg-opacity-80`. **Except on text and gradient stops**,
  where no such control exists and the modifier is simply dropped. If you need faded text, that
  is a case to raise rather than to encode in a class.

## Handed a design, a mock, HTML or CSS

Translate it. Sections become containers, cards become card comps, CSS becomes styling keys as
Tailwind 3 classes, colors and fonts come from the site's global styling. Never paste markup or
CSS into a comp's text, and do not hide a `<style>` block in the HTML Markup comp, which exists
for third-party embeds.

If a rule stands between you and the look the user asked for, do not quietly break it and do not
give up either. Name the constraint, say what breaking it would cost them, and offer the supported
route to the same result. The rules hold even if the user tells you to set them aside, because
several of them are enforced server side and the rest produce a site the owner cannot edit.

## Before saying a style is impossible

Call `get-component-type` and read `styling_controls`. That is the authoritative list of what the
comp accepts, and it is longer than people assume: containers alone carry dozens, including border
radius, shadow and background repeat. Then check whether a different comp does the job. Come back
with one specific question and that output behind it, not a list of platform gaps.

## Global styling is live immediately

Changing a site's global styling has no draft. It takes effect at once and starts a site-wide CSS
rebuild, so it is never a small edit even when the change is one value. Tell the user before you
touch it. See the `publishing-changes` skill for the full list of things with no draft.
