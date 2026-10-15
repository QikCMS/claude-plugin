---
name: publishing-changes
description: Publish QikCMS drafts, or check what would go live. Use when the user asks to publish, push live, ship or review pending changes on a QikCMS site, and before touching anything that has no draft, such as menus, redirects, global styling, the header, the footer, global variables or a featured image.
---

# Publishing in QikCMS

Two different things happen depending on what you write, and the difference matters to someone's
live website.

## Page and component edits are drafts

They do not appear to the public until published. That is the safety net for everything in the
`building-pages` skill: you can build a whole page and the owner still sees it before anyone else
does.

Publish tools refuse unless you pass `confirm` true. **Use that deliberately, in two steps:**

1. Run the publish tool **without** `confirm`. It reports what would go live.
2. Show the user that list and ask.
3. Only then run it with `confirm` true.

Never publish on your own initiative, and never as a tidy-up at the end of a build. "I finished
the page" is not permission to put it in front of the public.

## Some things have no draft and are live immediately

Writing any of these takes effect at once, with nothing to review and no publish step:

- menus
- redirects
- global styling
- the header and the footer
- global variables
- a featured image

**Tell the user before you touch one**, not after. And note that a global styling change starts a
site-wide CSS rebuild, so it is not a small edit even when the change is one value.

## Before you publish

- Say which site. Publishing is per site, and ids do not cross sites.
- Name what is in the set: which pages, and whether anything in it is one of the
  live-immediately items above.
- If the user asked for one page and the pending set contains three, say so. Drafts accumulate,
  and some of them may not be yours.

## After you publish

Say what went live and on which site. If a publish is refused, read the error rather than
retrying: a refusal usually names a validation problem in the content, which is a thing to fix
rather than a thing to force.
