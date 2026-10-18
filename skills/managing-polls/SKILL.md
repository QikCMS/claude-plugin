---
name: managing-polls
description: Create, read and edit polls on a QikCMS site. Use when the user asks to add a poll, change its question or answers, reword an option, open or close voting, restrict voting to signed-in people, change when results are visible, read vote totals, or delete a poll.
---

# Polls in QikCMS

None of the poll tools are in the main tool list. They sit behind `search_tools` and
`execute_tools`, so search the catalog for `poll` first and use the exact names and schemas it
returns. See `using-the-tool-catalog`.

Polls are a site feature and can be switched off. If these tools report the feature is disabled,
that is the site's configuration, not a fault, and it is the owner's to change in the CMS.

| Tool | What it is for |
| --- | --- |
| `list-polls` | ids, questions, whether each is running, vote totals |
| `get-poll` | one poll in full, every option **with its id** |
| `create-poll` | a new poll, stopped unless you say otherwise |
| `update-poll` | anything about an existing poll |
| `delete-poll` | confirm-gated, and it takes the votes with it |

## Votes are never yours to write

There is no tool that adds, edits or clears a vote, and there will not be: a tool that can add
ballots is a tool that can rig a poll. `entries` is not an argument anywhere.

Who voted is customer data and is never reported. What you get is the aggregate: a count per
option, a total, and `votes_for_removed_options`, which is votes belonging to answers an earlier
edit deleted. A non-zero number there is worth mentioning to the person, because it means votes
were already lost before you arrived.

## An option id is what its votes are attached to

This is the whole of the risk in this area. Ballots store the `option_id` they were cast for, and
the options live in a map keyed by that id.

`update-poll`'s `options` **replaces the entire list**. So:

- An option sent back **with its `id`** keeps that id, and therefore keeps its votes. This is how
  you reword an answer.
- An option sent as a plain string is **always a new option**, never a rename. Position is not
  matched. Renaming by sending a bare string in the same slot creates a new answer with no votes
  and deletes the old one with its votes.
- An existing option **left out of the list** is deleted, along with every vote cast for it.

So editing answers is always: `get-poll`, take the options with their ids, change the wording you
mean to change, send all of them back.

New ids are allocated above the current maximum, never reused, so votes can never be silently
reassigned to a different answer. Sending an `id` the poll does not have is refused with a message
telling you to leave the id out instead. The order you send them in is the order the site shows
them.

### The gate

When a change would delete options that already have votes, `update-poll` refuses unless you pass
`confirm` true:

1. Call it without `confirm`. It writes nothing and reports `would_remove_options`, each with its
   wording and vote count, plus `votes_that_would_be_discarded`.
2. Show the person that. If they only wanted the wording changed, the fix is to send each option
   back with its id, not to confirm.
3. Only call again with `confirm` true if they really want those answers and those votes gone.

Dropping an option that has no votes needs no confirm, and neither does anything else here:
renaming a poll or closing it discards nothing.

## There is no draft stage

Every save is live immediately. `update-poll` reports `changed` with the fields it actually wrote,
and tells you when nothing changed because every value sent already matched. Send only the fields
you are changing.

## Creating one

- The poll is created **stopped** (`is_active` false) so the person can look at it first. Leave it
  stopped, and say that is what you did.
- Options go in as plain strings on create. Two is the minimum.
- `name` is unique across the site and is the CMS-facing label, 3 to 100 characters. `question` is
  what people answer, 3 to 500.
- **Putting the poll on a page is a separate step**, and not one of these tools. Add the poll
  component to a page and point it at the poll. A poll nobody has placed is invisible to visitors.

## The settings worth knowing

- `view_results_type` is one of `instant`, `voted`, `end_date` or `hidden`. `voted` shows results
  only to someone who has voted, which is the usual choice for a poll meant to gather opinion
  rather than display it.
- `display_vote_count` shows the numbers alongside the results.
- `auth_only_voting` and `auth_only_viewing` are separate: a poll can be visible to everyone but
  votable only by signed-in people.
- `is_multiple_choice` lets one person pick more than one answer.
- `starts_at` and `ends_at` are `YYYY-MM-DD HH:MM:SS`, and either can be set to null to clear it.

## Deleting

`delete-poll` refuses without `confirm` true. The dry run reports the poll, how many votes it has
collected, and **how many components still show it**.

Treat it as permanent. There is no undo and no archive: the poll and every vote are gone, and any
page or comp pointing at it is left pointing at nothing, so check the pages it was on afterward.

If the person only wants it to stop collecting answers, that is `update-poll` with `is_active`
false. Offer that first.
