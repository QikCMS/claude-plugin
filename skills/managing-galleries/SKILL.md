---
name: managing-galleries
description: Create and fill photo and video galleries on a QikCMS site. Use when the user asks to add a gallery, put images or YouTube and Vimeo videos in one, caption or reorder its items, set its cover photo, change its URL, hide it, or delete it.
---

# Galleries in QikCMS

None of the gallery tools are in the main tool list. They sit behind `search_tools` and
`execute_tools`, so search the catalog for `gallery` first and use the exact names and schemas it
returns. See `using-the-tool-catalog`.

Galleries are a site feature and can be switched off. If these tools report the feature is
disabled, that is the site's configuration and the owner's to change in the CMS.

| Tool | What it is for |
| --- | --- |
| `list-galleries` | ids, slugs, visibility, cover image, **item counts**. Not the items |
| `get-gallery` | one gallery and every item in display order, with asset ids |
| `create-gallery` | a new gallery, empty and hidden |
| `update-gallery` | name, slug, summary, visibility. Not the contents |
| `delete-gallery` | confirm-gated |
| `add-gallery-asset` | put an image or a video in |
| `update-gallery-asset` | one item's caption, or a video's URL |
| `move-gallery-asset` | reorder, one place at a time |
| `remove-gallery-asset` | take an item out, leaving the image in the library |

## A gallery is a shell, and its assets are the content

`create-gallery` leaves you an **empty gallery, which renders as an empty page**. It is not
finished until you have added items. This is the mistake to avoid: creating a gallery, reporting
success, and leaving the person with a blank page.

So the job is always at least two calls, and `create-gallery` plus `add-gallery-asset` can go in
one `execute_tools` batch. Every create and update reply tells you the asset count, and
`list-galleries` carries it, which is there so you can see an empty one.

## Adding items

**An image must already be in the media library.** `add-gallery-asset` attaches one by
`image_id`; it does not upload. Find one with `search-media` or add one with `upload-media`
first. Two steps is what the CMS itself does, and it is what stops the same photo being uploaded
twice.

**A video is a YouTube or Vimeo URL and nothing else.** Pass `type` "video" with a `name` and a
`video_url`. No file is stored, the poster frame is derived from the URL, and the asset has no row
in the image library. Any other URL is refused, because the poster frame cannot be derived from
it. A video the site hosts itself is not this: upload it to the media library instead.

Items are always appended to the end. `name` is the caption, required for a video and optional for
an image, and `summary` is the longer caption under it.

## The cover photo sets itself, once

The **first image** added to a gallery becomes its cover photo if it has none, and the reply says
when that happened. There is no tool that sets the cover directly.

Internally the cover is a stored path rather than an image id, so the id it came from is not
recoverable and you are given the URL instead. Two consequences worth knowing:

- `remove-gallery-asset` on the item that is the cover **clears the cover**, and the gallery then
  has none until another image is added.
- You cannot repoint the cover at an existing item. To change it you are adding an image to a
  gallery that has no cover.

## Editing items

`update-gallery-asset` changes a caption, or a video's URL. It cannot change an item's **type**, or
**which image** an image item shows. To show a different picture, remove the item and add the other
one.

`move-gallery-asset` moves one item **up or down by one place**. There is no absolute position, so
moving something three places is three calls, which is exactly what the arrows in the CMS do. A
move already at the top or bottom does nothing and says so. Read the current order from
`get-gallery` first, and note that a batch of moves is reasoning about positions that are shifting
under you: prefer one call at a time and re-read.

Asset ids come from `get-gallery`, not from `list-galleries`, which deliberately does not list
items.

## URLs and visibility

The slug is the gallery's URL under `/galleries`, so pick it deliberately on create. The `-copy`
suffix is reserved by the CMS and is refused.

`update-gallery` refuses without `confirm` true whenever the gallery is **visible**, because
changing the slug of a live gallery moves its public URL. Call once without `confirm`, show the
person what would change, then call again with it.

Unlike a blog category rename, **the old gallery URL keeps working**: a redirect is created
automatically. Say so, because it is the reassuring half of the answer.

A gallery is created hidden. Set `is_visible` true with `update-gallery` once the person has
looked at it.

## Deleting

`delete-gallery` refuses without `confirm` true, and the dry run reports whether it is live and how
many items it holds.

It is not a reversible archive. The gallery is renamed ARCHIVED, its slug changes, its album and
access-group links are deleted, and its URL stops working. **The images themselves stay in the
media library**, so nothing is lost from the site's photos. If the person only wants it off the
site, that is `update-gallery` with `is_visible` false, and offer that instead.

To delete an image from the site entirely, that is `delete-image`, not anything here.
