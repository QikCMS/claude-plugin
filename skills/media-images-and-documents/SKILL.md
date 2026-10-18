---
name: media-images-and-documents
description: Find, upload and manage a QikCMS site's files. Use when the user asks to add or reuse an image, set alt text, upload a PDF or spreadsheet, link to a document, add a video or audio file, reference a YouTube or Vimeo video, or delete any of those.
---

# Images, documents and the Media library

None of these tools are in the main tool list. They sit behind `search_tools` and `execute_tools`,
so search the catalog for `media` or `document` and use the exact names and schemas it returns. See
`using-the-tool-catalog`.

## Three libraries, and the names do not tell you which is which

This is the thing to get right before anything else. `images`, `documents` and `media` are separate
tables with separate screens, separate upload paths and **separate ids**. An id from one is
meaningless to another. The oldest two tools are the misleading ones: `search-media` and
`upload-media` work on **images**, despite reading as though they covered everything.

| For | Tools |
| --- | --- |
| **Images** | `search-media`, `upload-media`, `update-image`, `delete-image` |
| **Documents**, the site's files | `list-documents`, `upload-document`, `update-document`, `delete-document` |
| **Video and audio** | `list-media-files`, `get-media-file`, `upload-media-file`, `update-media-file`, `delete-media-file` |

The rule that resolves it: anything named `*-media-file` is the video and audio library. Anything
named `*-image` or plain `*-media` is images.

`search-media` searches images by name and alt text, and can optionally include documents, so it is
the right first call when you are looking for something by name and are not sure which kind it is.

## Search before you upload, always

Every upload tool says this and it is the single most useful habit here. A site accumulates three
copies of the same price list and four of the same hero photo because nobody looked first.

- Images: `search-media`
- Documents: `list-documents`
- Video and audio: `list-media-files`

Searching also gets you the id and the public URL, which is what a component or a link needs.

## Uploads are not finished when the call returns

Both image and media-file uploads accept base64 contents or a public https URL, and both process in
the background.

For an image, optimization and thumbnails run asynchronously, so the record you get back is not
processed yet.

For video and audio it matters more, because an unprocessed file has **no playable version at
all**. Every row carries `is_processing`, `is_processed`, `is_missing` and `failed_reason`. Call
`get-media-file` and see `is_processed` true before you embed the file or tell anyone it is ready.

## Prefer a hosted video to an uploaded one

For anything long, pass the YouTube or Vimeo URL to `upload-media-file` rather than the file.
Nothing is stored but a downloaded poster frame, where an uploaded video is large, consumes the
site's storage and has to be processed first.

A hosted entry has `is_external` true, `external_url` and `external_type` set, and no file of its
own. Do not treat the two cases the same.

**Rename a hosted video immediately after adding it.** They are all created named "YouTube Video"
or "Vimeo Video", and a library full of those is unusable. That is `update-media-file`.

## Alt text is the part that gets skipped

`update-image` sets alt text, renames an image, or moves it to a folder. Alt text is the one that
matters: it is what screen readers announce and what search engines read, and an image uploaded
without it has none. Set it when you upload, or set it straight after.

Write alt text as a description of the picture, as the business would. Never mention AI, a model or
a vendor, and never name a file for how it was produced.

## What cannot be swapped

None of `update-image`, `update-document` or `update-media-file` can replace the underlying file.
For an image the reason is worth repeating to a person who asks: every page using that image would
change at once, so it is deliberately a CMS-only action. For the other two, upload the new version
and update whatever links to it.

Renaming a document does **not** change its public URL.

## Deleting, and the one that is genuinely dangerous

All three delete tools refuse without `confirm` true and report what would go on the dry run. Two
of them tell you how many places on the site use the thing:

- `delete-image` reports a usage count. Deleting an image that is in use does not remove it from
  those pages, it leaves them with a broken picture. A count above zero is a reason to stop and
  ask.
- `delete-media-file` reports a usage count too, with the same consequence: the places showing it
  show nothing.

`delete-document` is the dangerous one, because **nothing in the CMS records which pages link to a
file**. There is no usage count to read. A link anywhere on the site pointing at that document
breaks silently and nobody is told. Check with the person before confirming, and if they are
unsure, leave it.

Two narrower alternatives worth offering instead of a delete:

- To take an image out of one gallery and leave it on the site: `remove-gallery-asset`.
- To stop using something without destroying it: leave it in the library. An unused file costs
  storage and nothing else.
