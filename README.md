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
| `skills/publishing-changes` | Drafts versus the things that go live the moment they are written |
| `skills/copying-between-sites` | Reusing a layout across sites, where ids do not carry over |
| `commands/build.md` | `/qikcms:build`, an entry point that loads the site and the rules first |

The build rules themselves are **not** in this repository. They are served by the CMS through the
`get-build-guide` and `get-site-conventions` tools, so they are maintained in one place and cannot
fall out of step with the platform. The skills tell Claude to call them; they do not restate them.

## Install

From your shell:

```bash
claude plugin marketplace add CodyPChristian/tba-qikcms-claude-plugin
claude plugin install qikcms@qiksolutions
```

Or in one step from inside a session, on Claude Code 2.1.275 or later:

```text
/plugin install qikcms --marketplace CodyPChristian/tba-qikcms-claude-plugin
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

- `repository` in `plugin.json` points at the current private repo and needs updating when this
  moves to its final home
- Anthropic's directory reads `icon`, `documentationUrl`, `supportUrl`, `privacyPolicyUrl` and
  `termsOfServiceUrl` from `plugin.json`. None are set yet; set them before submitting
- The repository must be public before a directory listing goes live
