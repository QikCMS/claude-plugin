---
name: writing-blog-posts
description: Create, read and edit blog posts on a QikCMS site. Use when the user asks to write, publish, retitle, recategorize, backdate, hide or delete a blog post, or to change a post's body, summary, categories, SEO fields or featured image.
---

# Blog posts in QikCMS

None of the blog tools are in the main tool list. They sit behind `search_tools` and
`execute_tools`, so search the catalog for `blog` first and use the exact names and schemas it
returns. See `using-the-tool-catalog`.

| Tool | What it is for |
| --- | --- |
| `list-blog-posts` | ids, slugs, visibility and categories. No bodies |
| `get-blog-post` | one post in full, body included |
| `create-blog-post` | a new post, hidden unless you say otherwise |
| `update-blog-post` | anything about an existing post |
| `delete-blog-post` | confirm-gated, and not an undo |
| `list-blog-categories` | the category tree, and the ids a post needs |
| `create-blog-category`, `update-blog-category` | the tree itself |

## Read before you write

`update-blog-post` replaces the entire body. There is no block-level patch. So editing part of a
post is always: `get-blog-post`, change the blocks you mean to change, send all of them back.
Anything you leave out is gone.

`list-blog-posts` is the cheaper call when you only need to find an id, and its `summary` is empty
on plenty of posts, so it is not a substitute for reading the post.

## The draft, which is where the risk is

A post can carry an unsaved draft: the owner's own work in progress in the CMS editor.

- `get-blog-post` hands you the DRAFT when there is one, because that is the body the owner is
  looking at. `content_source` says which body you got, `draft.has_draft` says whether there is
  one, and `include_published` true returns the live body as well.
- A body sent to `update-blog-post` lands wherever that post's body already lives. On a post with a
  draft it goes into the draft and is **not published**, waiting for the owner's next Save.
  `body_target` in the response says `draft` or `published`. Believe that field, and tell the person
  which it was.
- The draft body is replaced wholesale too, so writing into a draft without reading it first
  destroys the rest of the owner's unsaved work, and drafts are not revisioned.
- `publish_body` true forces the body live on a post that has a draft. Their draft stays in place,
  so their next Save republishes it over your change. Only pass it when the person has asked for
  that, knowing it.

An empty draft is a real state, not a fault. If `draft_blocks` is 0 while the live post reads fine,
that is the owner's own unsaved emptying. Do not repair it by writing the published body back
without asking.

## Everything except the body is live immediately

Title, slug, summary, SEO fields, categories, featured image and visibility have no draft layer. On
a post that is currently live, `update-blog-post` refuses unless you pass `confirm` true:

1. Call it without `confirm`. It writes nothing and reports `would_change`.
2. Show the person that list.
3. Call again with `confirm` true.

A hidden post needs no confirm, and neither does a body-only edit that lands in a draft. Changing
`slug` changes the live URL.

## Creating one

- The post is created **hidden** unless you pass `is_visible` true. Leave it hidden so the owner can
  review it, and say that is what you did.
- `content` is the editor block structure, not HTML and not Markdown:
  `{"blocks": [{"type": "header", "data": {"text": "...", "level": 2}}, {"type": "paragraph", "data": {"text": "..."}}]}`
- The slug is derived from the name when you omit it. `name` is unique across the site, so check
  `list-blog-posts` before writing one that would clash.
- Set `featured_image_id` from `search-media` or `upload-media`. Without one the post looks wrong in
  listings and when it is shared.
- `created_at` is the post DATE, read as US Eastern, so it is how you backdate or forward-date a
  post.
- `seo_name` shares a 60 character budget with the site name that gets appended, so subtract the
  site name and write to what is left. `seo_description` truncates in search results around 160.

## Categories

`list-blog-categories` returns the tree with its ids. A post's categories are set on the post, in
`create-blog-post` or `update-blog-post`, and sending `categories` replaces the whole set rather
than adding to it.

Renaming a category changes its URL and leaves no redirect, so any existing link to it breaks. Say
so before renaming one. A category name may not contain the word "blog", and may not match an
existing top-level category.

## Deleting

`delete-blog-post` refuses without `confirm` true, and the dry run reports what would be lost. Treat
it as permanent: the post loses its comments, its category links and its featured image, its title
is prefixed ARCHIVED, and its URL stops resolving. If the person only wants it off the site, that is
`update-blog-post` with `is_visible` false, and offer that instead.

## The copy itself

Write as the business. Never mention AI, an assistant, a model or a vendor in a title, body,
summary, SEO field, slug or image name, and never label a post as generated. Titles and slugs also
may not contain the site's own name or a pipe character, which the API enforces.
