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

Then the content areas, each a full set of list, get, create, update and delete. Each has a skill
of its own with the rules that matter, so read that rather than working it out from the schemas:

| Area | The trap, in one line | Skill |
| --- | --- | --- |
| Blog posts and categories | A post can hold an unsaved draft, and a body write lands in it | `writing-blog-posts` |
| Galleries | A gallery is a shell and renders empty without its assets | `managing-galleries` |
| Polls | An option's id is what its votes are attached to | `managing-polls` |
| Images, documents, video and audio | Three separate libraries, and the names do not say which is which | `media-images-and-documents` |
| Custom content | A type's slug is a URL prefix, so changing it moves every item under it | `custom-content-types` |

The media one is worth previewing here, because it is the mistake you can make before you know you
have a question: `search-media` and `upload-media` are **images** despite reading generic,
`list-documents` and friends are the site's **files**, and anything named `*-media-file` is the
**video and audio** library, a different table with its own ids.

Before telling the user something cannot be done, search the catalog with an empty query and
look. "There is no tool for that" is a claim worth checking, and it is cheap to check.
