# QikCMS plugin for Claude

Build and edit [QikCMS](https://qikcms.com) websites from Claude.

The plugin connects Claude to the QikCMS gateway, one endpoint that reaches every site on your
account, and adds the workflow for building pages the way this CMS expects: out of each site's own
components, not out of HTML.

## What is in it

| Component | What it does |
| --- | --- |
| `.mcp.json` | Points Claude at `https://mcp.qikcms.com/mcp`. Sign-in is OAuth, so there is no token to paste |
| `skills/building-pages` | The order of operations for a build, and the constraints the CMS enforces |
| `skills/building-forms` | Forms, which use a separate component vocabulary from pages |
| `skills/writing-blog-posts` | Posts, their draft layer, and which fields are live the moment they save |
| `skills/dynamic-content-comps` | Why a grid of products or posts is a dynamic comp, not hand-built cards |
| `skills/styling-and-tailwind` | Tailwind 3 styling keys, and what the API refuses or silently converts |
| `skills/using-the-tool-catalog` | Reaching the ~90 tools behind search_tools and execute_tools |
| `skills/publishing-changes` | Drafts versus the things that go live the moment they are written |
| `skills/copying-between-sites` | Reusing a layout across sites, where ids do not carry over |
| `skills/recovering-from-refusals` | What a refused call means, and who can actually fix it |
| `commands/build.md` | `/qikcms:build`, an entry point that loads the site and the rules first |

The build rules themselves are **not** in this repository. They are served by the CMS through the
`get-build-guide` and `get-site-conventions` tools, so they are maintained in one place and cannot
fall out of step with the platform. The skills tell Claude to call them; they do not restate them.

## Install

From your shell:

```bash
claude plugin marketplace add QikCMS/claude-plugin
claude plugin install qikcms@qiksolutions
```

Or in one step from inside a session, on Claude Code 2.1.275 or later:

```text
/plugin install qikcms --marketplace QikCMS/claude-plugin
```

Then start Claude Code and run `/mcp` to sign in. A browser opens; if it cannot, Claude Code
prints a URL and takes the redirect URL pasted back.

When you approve the connection you choose which of your sites it may work on. You change that
later, or disconnect it, under **Account, Connected apps** in the QikCMS portal.

## Who can use it

Company owners and QikCMS platform administrators. The gateway re-checks membership on every call,
so access removed in the portal takes effect on the next request rather than whenever a token
expires.

A person who is not an owner of a company gets refused even with the plugin installed, including a
developer working on someone else's account.

## Requirements

- A QikCMS account with at least one installed site
- Each site on QikCMS 5.15.7 or later, which is the first release with the endpoint the gateway
  needs. `list-sites` marks anything older rather than letting you pick it and fail

## Before the public listing

- All five directory listing fields are set. `documentationUrl` and `supportUrl` both point at
  `https://docs.qikcms.com`, which is titled "QikCMS Support Docs" and serves both purposes.
  `support.qikcms.com` is not used because it currently redirects to itself and loops; point
  `supportUrl` there once that host is fixed, if you would rather separate the two
- `docs.qikcms.com` answers 200 and sends `/` to `/dashboard`. Worth opening it signed out before
  submitting, so a reviewer does not land on something that looks like an empty app shell
- Submit from the organization that should own the listing long term. The first organization to
  submit a given repository folder holds it, and a second organization's submission of the same
  one is refused
- The gateway goes in separately as an MCP connector, which needs only its URL. Submitted from the
  same organization, the two listings can be paired
