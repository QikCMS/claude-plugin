---
name: custom-content-types
description: Work with a QikCMS site's own content types, such as Sermons, Podcasts or Case Studies, and their items and categories. Use when the user asks to add or edit one of these items, organize them into categories, change a type's URL prefix or visibility, or define a new kind of content.
---

# Custom content in QikCMS

None of these tools are in the main tool list. They sit behind `search_tools` and `execute_tools`,
so search the catalog for `custom content` and use the exact names and schemas it returns. See
`using-the-tool-catalog`.

Custom content is the owner's **own** content types: Sermons, Podcasts, Case Studies, whatever the
site needed that the CMS does not ship. Each type owns a URL prefix and holds its own items and its
own category tree.

These are not page component types. Those are `list-component-types`.

Start with `list-custom-content-types`. It gives the `content_type_id` every other tool here needs,
and its item and category counts tell you whether a type is actually in use.

| Level | Tools |
| --- | --- |
| **Types** | `list-custom-content-types`, `create-`, `update-`, `delete-custom-content-type` |
| **Items** | `list-custom-content-items`, `get-`, `create-`, `update-`, `delete-custom-content-item` |
| **Categories** | `list-custom-content-categories`, `create-`, `update-`, `delete-custom-content-category` |

## Do not create a type without asking

Creating a type adds a whole section to the site: its own URL prefix, its own index page, its own
admin screen. Most content does not need one. Pages, blog posts, products, events and the directory
already exist, and a new type is the wrong home for anything that fits one of those.

So when someone asks for "a section for X", first work out whether X is really a new kind of
content or just a page or a set of blog posts. Ask them. If a type really is right, the slug
becomes the URL prefix for every item under it and cannot be one the site already uses: `blog`,
`products`, `events`, `cart`, `account` and similar are refused. It is created hidden.

## A type's slug moves every item under it

This is the trap in this area. `update-custom-content-type` refuses without `confirm` true whenever
the slug changes or the type is visible:

1. Call it without `confirm`. It writes nothing and reports **how many items would move**.
2. Show the person that number.
3. Call again with `confirm` true.

Old URLs keep working, because the CMS writes the redirects. Say so, because it is the half of the
answer that makes the change safe.

`settings` is the type's display configuration and **replaces** what is there. Read it from
`list-custom-content-types` and send it back whole with your change applied.

## Items: no draft stage, and the body is replaced wholesale

Two rules, and they compound.

**There is no draft layer.** What `get-custom-content-item` returns is what is live, and what you
write goes live. This is unlike a blog post, which can hold an unsaved draft.

**`content` replaces the entire body.** There is no block-level patch. So editing part of an item is
always: `get-custom-content-item`, change the blocks you mean to change, send all of them back. A
block you leave out is deleted from the live page immediately, with no draft to catch it and no
revision to recover it from.

`get-custom-content-item` returns `content` in exactly the shape the update takes, and the same
field names, so it is a read, edit, write round trip rather than a translation.

`list-custom-content-items` does not carry bodies and its `summary` is often empty, so it is for
finding an id, not for reading what an item says.

Other item rules:

- The body is editor.js blocks, the same shape a blog post body takes, and it is optional at
  creation.
- The slug is unique **within its own type**, not across the site, and it is slugified for you.
- Items are created **hidden** unless you say otherwise. Leave them hidden so the person can read
  one before anyone else can, and say that is what you did.
- `categories` is a list of ids from `list-custom-content-categories`. Leaving it out keeps the
  existing ones; **sending it replaces them**.
- `update-custom-content-item` refuses without `confirm` true whenever the item is live. Changing
  the slug of a visible item moves its URL and leaves a redirect behind.

## Categories are a tree

Each carries a `parent_id`, so a category can sit under another. Pass `parent_id` to nest one, or
leave it out for a top-level one. The slug and URL are built by the CMS.

A category name only has to be unique **among its siblings** in the same type, so the same name can
appear under two different parents. Read the tree before adding one.

`update-custom-content-category` renames, re-parents, or changes the image. Both renaming and
re-parenting change the URL path of the items filed under it, so read the item count in the reply
and tell the person. A category cannot move to a different content type, and cannot be nested
inside itself or one of its own descendants.

## Deleting, in order of how much it takes with it

**`delete-custom-content-category`** is the mildest. Confirm-gated, and the dry run reports two
numbers: how many child categories would go, because deleting a category **deletes its whole
branch**, and how many items would be left unfiled. The items themselves are kept and their URLs
keep working. They just stop being in this category, which can drop them off listings. Show the
person both numbers.

**`delete-custom-content-item`** is confirm-gated and the dry run reports whether it is live and
how many blocks its body holds. Its URL stops working immediately and **no redirect is written**,
so anything linking to it breaks. If the person only wants it off the site, that is
`update-custom-content-item` with `is_visible` false. Offer that instead.

**`delete-custom-content-type`** should almost never be confirmed. It takes down the type's index
page, its routes, and the public URL of every item under it. Run it without `confirm` to see how
many items and categories are involved, and then propose the reversible option:
`update-custom-content-type` with `is_visible` false takes the section off the site and keeps the
content. Say that is what you are recommending and why.

## The copy itself

Write as the business. Never mention AI, an assistant, a model or a vendor in a type name, item
name, body, summary, slug or category name, and never label an item as generated.
