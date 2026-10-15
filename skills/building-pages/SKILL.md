---
name: building-pages
description: Build, lay out or restyle a page on a QikCMS website. Use when the user asks to create a page, add or rework a section, change a layout, or apply a design to one of their QikCMS sites, and before the first write of any build. Covers choosing which site, the reads to do first, and how a page is composed from a site's own components.
---

# Building pages in QikCMS

A QikCMS page is a tree of that site's own components. It is not markup. The single most
common failure is treating it as a web page: writing HTML into a component's text, inventing a
component source, or composing a page one element at a time. This skill is the order of
operations that avoids that.

## The rules live on the server, not here

Two tools carry the authoritative rules, and they are maintained with the CMS rather than with
this plugin:

- **`get-build-guide`** is the house rules, written against real builds that got things wrong.
- **`get-site-conventions`** is this site's fonts, colors, global variables and content tokens.

**Call both before your first write on a site.** Do not substitute what is written below, and do
not assume a second site inherits the first site's conventions. If either call fails, say so
rather than building from guesswork.

Everything in this skill is workflow. Everything about *how* to build is in those two documents.

## Which site

Call `list-sites` first. Every site-scoped tool needs `site_id`, and it is the one thing a site
cannot tell you about itself.

`site_id` accepts the id `list-sites` returns, the site's exact name, its primary domain or its
subdomain, so you can usually pass whatever the user called it. It does **not** accept a partial
name. If you are unsure which site is meant, call `list-sites` with `search` and ask. These tools
write to live websites, so a near-miss edits the wrong customer's site.

Say which site you are working on before you touch it, and say it again if you switch.

Page, component, form, blueprint and global comp ids belong to **one** site and are never valid
on another.

## The order

1. `list-sites`, then `site-overview` and `list-pages` for the site in question.
2. `get-build-guide` and `get-site-conventions` for that site.
3. `list-component-types` to see the vocabulary, and `get-component-type` for the keys of
   anything you intend to use. Component sources look like `sections.hero.types.type2`, and both
   sources and config keys come from these calls rather than from memory.
4. **Reply with a plan and stop.** The sections in order, and the component source for each. Wait
   for a go-ahead before writing anything.
5. Build a section per call with `apply-stack`, or copy a saved layout with `apply-blueprint`.
   Tell the user what you created as you go.

`search_tools` and `execute_tools` reach the rest of the tool surface. `execute_tools` takes
`site_id` once at the **top level** and it applies to the whole batch; a batch that puts
`site_id` inside one of its calls is rejected without running anything. Batches cap at ten calls
and stop on the first error, so keep them small.

## What not to do

These are mechanical limits of the CMS, not preferences, and the server enforces several of them:

- **No HTML, CSS, Tailwind classes or Markdown in a component's content.** A text value is a
  plain string or an editor.js document. If you are about to write `<div>` or `class="..."` into a
  component, stop.
- **Do not invent a component source, a config key or a styling key.** Read them.
- **Prefer the prebuilt sections** (hero, header, cta, content, card, testimonial, faq, logo
  cloud, tabs, slider) over hand-composing from containers and text elements.
- **Do not set styling values unless asked for something specific.** What you create already
  inherits the site's global styling.
- **Handed HTML, a template or a mock? Translate it**, into components and styling keys. Do not
  paste markup, and do not hide CSS in the HTML Markup comp, which is for third-party embeds.
- **Never mention AI, an assistant, a model or a vendor in anything you save**: not page copy,
  headings, alt text, image or file names, component names, page names, slugs or blog posts. The
  site speaks as the owner's business to their customers.

If a write is refused, read the error. It names the paths and shapes that actually exist, so fix
the payload from it. Never retry the same payload unchanged, and never fall back to raw HTML or
to one text component holding the page.

## Before calling something impossible

Call `get-component-type` and read its `styling_controls`, which is the authoritative list of
what a component accepts. Containers alone offer dozens, including border radius, shadow and
background repeat. Then check whether a different component does the job. Bring one specific
question with that output behind it rather than a list of platform gaps.

If the user asks you to break a build rule to match a design, name the rule, say what breaking it
would cost them, and offer the supported route to the same look.

## Related skills

- **`styling-and-tailwind`** before you set any styling key. Several values are refused or
  silently rewritten, which is worth knowing before you send them.
- **`dynamic-content-comps`** before you build a grid of cards for products, posts, events or
  anything else the site already holds as records.
- **`building-forms`** for forms, which use a different component vocabulary entirely.
- **`using-the-tool-catalog`** when what you need is not one of the thirteen listed tools, which
  includes reading a page's real tree and editing a single comp.
- **`recovering-from-refusals`** when a call comes back refused.

## Publishing

Page and component edits land as **drafts**. Publishing is a separate, deliberate act and needs
the user's say-so. See the `publishing-changes` skill before you publish anything, because some
things have no draft at all and go live the moment they are written.
