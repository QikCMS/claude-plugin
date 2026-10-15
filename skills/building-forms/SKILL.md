---
name: building-forms
description: Build or edit a form on a QikCMS site. Use when the user asks to create a form, add or change fields, build a contact, booking, quote or signup form, or mentions apply-form-stack. Forms use a different component vocabulary from pages and the two must never be mixed.
---

# Building forms in QikCMS

A form is not a page. It has its **own component vocabulary**, and the CMS is explicit that the
two must not be mixed. A page component source in a form stack, or a form field source in a page
stack, is wrong even when it looks plausible.

## The vocabulary is behind the catalog

`list-form-component-types` is **not** one of the directly callable tools. It sits behind
`search_tools` and `execute_tools` along with most of the tool surface, so reaching it is a step
people miss:

1. `search_tools` with a query like `form component types`.
2. `execute_tools` with that exact tool name to get the sources.

Every item in a form stack needs a `source` from that list. Do not reuse a source you saw in
`list-component-types`, which is the page vocabulary.

## The order

1. `list-forms` to get the `form_id` every other form tool needs. It accepts `search` and `limit`.
2. Get the form vocabulary, as above.
3. `apply-form-stack` with `form_id` and `stack`. It builds the whole field tree in one call,
   nesting included, and it is the tool to reach for whenever you are adding more than a single
   field.

## What will catch you out

**`apply-form-stack` APPENDS.** The stack lands after whatever is already on the form, inside
`step` when the form uses steps. It does not replace. So either start from a freshly created
form, or remove what you are replacing first. Building a form twice gives you every field twice.

**`config` is an override map, not a blob.** Config is seeded from the component library on every
item, so pass only the keys you are changing. Keys the library does not define come back under
`ignored`, which is worth reading: it is how you learn you guessed a key name.

**Only a columns container takes children.** Nothing else nests.

**Name and address inputs already have sub-fields.** They always get their own, so `children` on
one of those does not add fields, it only adjusts the existing sub-fields by name.

**Any rule violation rolls the whole stack back.** A refusal means nothing was written, so fix
the payload from the error rather than retrying it or applying it piecemeal.

`parent_id` nests the stack inside an existing item instead of at the form root, and `step`
targets a step on a multi-step form.

## Submissions are not here

`list-forms` returns the form **builder** only. Form submissions are never available through this
server, by design: this connection works on website content and holds no customer data. If the
user asks to read, export or summarize submissions, say that plainly and point them at the QikCMS
portal rather than looking for a tool.

## Drafts

Form edits land on the form **draft**, the same as page edits. Publishing is separate and needs
the user's say-so. See the `publishing-changes` skill.
