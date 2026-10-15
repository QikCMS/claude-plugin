---
name: recovering-from-refusals
description: Work out what to do when a QikCMS tool call is refused or errors. Use when a QikCMS call comes back with an access, connection or site error, when a site is not in list-sites, when a site is marked as needing an update, or before retrying any failed QikCMS call.
---

# When a QikCMS call is refused

Refusals here are specific and they already name the recovery, so the first move is to read the
message rather than retry. The important distinction is **who can fix it**, because most of these
cannot be fixed by trying again or by trying something else.

## Nothing is fixed by retrying

An access refusal is a statement about the world, not a transient failure. The same call will be
refused the same way a second later. Retrying wastes the person's time and, worse, makes it look
like the tool is flaky when the answer was clear.

A refusal is also **not** a cue to try a different site. If the site you were asked about is not
available, substituting another one risks editing somebody else's website. Stop and say so.

## Three kinds, and what each means

**You can fix it yourself.**

- *Site not found or not connected*, with a list of what is connected. You used a name or id that
  did not match. Pick one of the listed `site_id` values, or call `list-sites` with `search`.
- *`site_id` is required when more than one site is connected*. Ask which site, or pass one.
- *More than one connected site is called X*. Two sites share that name. The message names both
  `site_id` values; ask the person which one.
- A **validation** error from a write. The message names the paths and shapes that actually
  exist, so fix the payload from it. Never resend the same payload unchanged.

**The person has to fix it, in the QikCMS portal.**

- *No sites are connected*. They approved the connection without choosing a site, or removed them
  all. They add one under Account, Connected apps.
- *This connection has been revoked. Reconnect it from QikCMS, Account, Connected apps.* The
  connection is dead. Nothing will work until they reconnect, and reconnecting asks them to choose
  sites again.
- *You no longer have access to that site*. Their access was removed in the portal while the
  connection stayed. The site owner restores it. Membership is re-checked on every call, so this
  takes effect immediately and is not a stale cache.

**Somebody else has to fix it.**

- *Your account is not permitted to work on sites through this connection* or *limited to company
  owners and QikCMS admins*. This connection is for company owners and QikCMS platform admins. If
  the person is a developer on someone else's account, they cannot use it and no amount of
  reconnecting will change that. Say so plainly rather than suggesting workarounds.
- *Your QikCMS account has not been set up on that site*, or a session could not be started. The
  site side is not ready for them. A QikCMS admin adds them to the site.
- *Something went wrong on our side of this connection.* A gateway or site fault, already
  reported. Tell the user rather than retrying in a loop, and move on to something else if there
  is something else to do.

## A site listed but marked as needing an update

`list-sites` includes sites that cannot be worked on yet, flagged with `needs_update` and a note,
rather than hiding them. That is deliberate: "that site is missing" is a worse answer than "that
site needs updating first". Do not try to build on one. Tell the person that site is on an older
QikCMS release and has to be updated before this connection can reach it.

## How to report a refusal

Say what was refused, which site, what it means, and the single next step for whoever can act.
One or two sentences. Do not paste the raw error and do not speculate about causes the message
did not give you.
