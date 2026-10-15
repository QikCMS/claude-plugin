---
name: copying-between-sites
description: Copy a page, section or layout from one QikCMS site to another. Use when the user asks to reuse, duplicate, clone or copy content, a section or a layout between two of their QikCMS sites, or to make one site look like another.
---

# Copying between QikCMS sites

The gateway reaches several sites through one connection, which makes this look easier than it
is. The thing to understand first: **ids do not cross sites.**

A page id, component id, form id, blueprint id or global comp id belongs to exactly one site.
Passing one to another site is not a permission error that stops you, it is an id that means
something different or nothing at all on the other side.

## The route that works

1. `list-sites`, and confirm with the user which site is the source and which is the target.
2. `get-page-stack` on the **source** site to read the real tree, with its component ids.
3. `apply-stack` on the **target** site to build it there.

That is a read followed by a build, not a move. Nothing is transferred; the second site gets its
own components with its own ids.

## For a layout you will reuse more than once

Save it as a blueprint and use `apply-blueprint` on the target. A blueprint is the supported way
to carry a layout, and it survives being used again next month. Copying a stack by hand each time
is the thing blueprints exist to replace.

## What does not come with it

- **Assets stay on the source site.** Images and documents referenced in a copied stack still
  live where they were uploaded. Say so rather than assuming they moved, and re-upload to the
  target site if the owner wants them independent.
- **Conventions do not come with it either.** Call `get-site-conventions` for the **target** site.
  The source site's fonts, colors and global variables are not the target's, so a stack that
  looked right on one can land wrong on the other. This is the most common way a copy comes out
  looking broken.
- **Forms** are per site. A copied section referencing a form needs that form to exist on the
  target, so check `list-forms` there first.

## One site per batch

`execute_tools` takes `site_id` once at the top level and it applies to every call in the batch. A
batch that puts `site_id` inside one of its calls is rejected without running anything, so a
single batch cannot span the source and the target. **Read from one site, then write to the other,
in separate calls.**

That is deliberate: a half-applied batch across two sites would have changed two customers'
websites with no clean way to describe what happened.

## Tell the user which way round it is

Before writing anything, say out loud which site is being read and which is being written, and
wait for confirmation. Copying in the wrong direction overwrites the site they wanted to keep,
and the write lands as a draft but the work of undoing it is still theirs.
