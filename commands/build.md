---
description: Start a QikCMS build with the site chosen and the build rules loaded
argument-hint: "[site] [what to build]"
---

# Start a QikCMS build

Open a build on a QikCMS site properly, so the first write is not a guess.

The user's request, if they gave one: `$ARGUMENTS`

Do this in order, and stop where it says to stop.

1. Call `list-sites`. If the request names a site, resolve it: `site_id` accepts the id,
   the site's exact name, its primary domain or its subdomain. If it names no site, or names one
   that is not in the list, ask rather than picking. Never substitute a different site.

2. For the chosen site, call `site-overview` and `list-pages`, then `get-build-guide` and
   `get-site-conventions`. The last two are the authoritative rules and this site's palette.
   Follow them over anything you remember about QikCMS.

3. Call `list-component-types`, and `get-component-type` for anything you intend to use, so every
   component source and config key you write comes from the site rather than from memory.

4. **Reply with a plan and stop.** State the site, the sections in order, and the component
   source for each. Do not write anything yet. Wait for a go-ahead.

Once the user agrees, build a section per call with `apply-stack` or `apply-blueprint`, and tell
them what you created as you go. Page edits land as drafts; do not publish without being asked.

The `building-pages` skill has the full workflow and the constraints, including what a component's
text may contain. Read it if you have not.
