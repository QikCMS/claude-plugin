---
name: using-the-tool-catalog
description: Find and run a QikCMS tool that is not in the main tool list. Use when a QikCMS task needs something beyond the fourteen listed tools, such as reading a page stack, creating a page, editing one component, listing blueprints, menus, redirects, global styling, collections, blog posts, galleries, polls, images, documents, video and audio, or the owner's own custom content types, or when a qikcms:// resource read fails.
---

# Reaching the rest of the QikCMS tools

Fourteen tools are listed directly. Most of the surface, roughly another hundred and twenty, sits
behind two of them. If the thing you need is not in the list, it almost certainly exists.

## The pair

**`search_tools`** returns exact tool names, descriptions and complete input schemas. It takes
`query` and `limit`, and an **empty query browses the whole catalog**, which is the right move
when you do not know the vocabulary yet.

**`execute_tools`** runs what you found:

```json
{"calls": [{"name": "tool_name", "arguments": {"key": "value"}}]}
```

Use the exact names and arguments `search_tools` returned. Do not guess a tool name, and do not
guess an argument: the schema comes back with the search result for a reason.

## The limits, which are enforced

- **Ten calls per batch.** `calls` has `maxItems: 10`.
- **Execution stops on the first error.** Later calls in the batch do not run.
- **`site_id` goes once at the top level** of `execute_tools` and applies to every call in the
  batch. A batch that puts `site_id` inside a call's `arguments` is rejected without running
  anything, so one batch cannot span two sites.
- **No chaining inside a batch.** The calls are independent and run in order. If one needs a
  previous call's result, send a second `execute_tools` with a new `calls` array.

Keep batches small. A batch of ten where the second fails tells you less than two batches of
three.

## Do not try to read a qikcms:// resource here

The CMS instruction text mentions `qikcms://build-guide`, `qikcms://site-conventions`,
`qikcms://component-library` and `qikcms://form-component-library`. **This connection exposes no
resources at all**, because it fronts many sites rather than one. Those reads will not work, and
a failed resource read is not a sign that anything is wrong.

Use the tool equivalents, which carry the same content:

| Instead of | Call |
| --- | --- |
| `qikcms://build-guide` | `get-build-guide` |
| `qikcms://site-conventions` | `get-site-conventions` |
| `qikcms://component-library` | `list-component-types` |
| `qikcms://form-component-library` | `list-form-component-types`, via the catalog |

## Things that live behind the catalog

Worth knowing so you look rather than conclude a thing is impossible: reading a page's real tree
(`get-page-stack`), creating and editing pages, editing a single comp (`update-component`,
`add-component`, `move-component`, `remove-component`), blueprints (`list-blueprints`), the form
vocabulary, menus, redirects, global styling, global variables, the header and footer, page FAQs,
collections where that feature is on, and the publish tools.

Then the content areas, each a full set of list, get, create, update and delete:

- **Blog posts and their categories.** Categories are a tree, and a post's categories are set on
  the post itself rather than on the category.
- **Galleries.** A gallery is only a shell: its assets are the content, which is what the asset
  tools are for. A gallery created without them renders as an empty page.
- **Polls.** Votes come back as totals and can never be written.
- **Custom content**, meaning the owner's own content types, their categories and their items. A
  type's slug is a URL prefix, so changing it moves every item under it.

Media is three separate things, and the names do not say so:

| For | Use |
| --- | --- |
| Images | `search-media` and `upload-media` despite the generic names, plus `update-image` and `delete-image` |
| Documents, the site's files | `search-media` finds them; `list-documents`, `upload-document`, `update-document` and `delete-document` manage them |
| Video and audio | `list-media-files`, `get-media-file`, `upload-media-file`, `update-media-file`, `delete-media-file`, a different table from images |

Before telling the user something cannot be done, search the catalog with an empty query and
look. "There is no tool for that" is a claim worth checking, and it is cheap to check.
