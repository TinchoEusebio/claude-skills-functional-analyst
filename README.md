# Claude Code skills for functional analysts

Three [Claude Code](https://claude.com/claude-code) skills for the day-to-day writing
work of a functional analyst / product owner: drafting epics, writing Jira tickets, and
deriving test cases from acceptance criteria.

They encode a *format and a process*, not a domain. Nothing here is specific to a
company: each skill has one **`## Your repo context — EDIT THIS`** section that you fill
in with your own repositories and Jira project keys before using it.

## The skills

| Skill | What it does |
|-------|--------------|
| [`write-ticket-description-criteria`](skills/write-ticket-description-criteria/) | Phase 1 of a ticket: title, Summary and Acceptance Criteria, typed as Bug / Story / Task. Enforces vertical slicing (one story = one ticket across all layers) and refuses to pad the ticket with sections nobody asked for. |
| [`write-test-cases`](skills/write-test-cases/) | Phase 2 of the same ticket: an exhaustive `### Proposed test cases` section derived from the AC, grouped by functional area, plus a separate list of acceptance criteria the cases revealed as missing. |
| [`write-epic`](skills/write-epic/) | A full functional spec for work spanning several stories and repos, following a fixed 12-section template (`BR-` business rules, `FR-` functional requirements, `AF-` alternative flows). See [the template](skills/write-epic/references/epic-template.md). |

The intended flow is: `/write-epic` to scope a large initiative → `/write-ticket-description-criteria`
per story, iterating until the AC are settled → `/write-test-cases` to close it out.

## Install

Copy the skill folders into your personal skills directory:

```bash
# macOS / Linux
cp -r skills/* ~/.claude/skills/

# Windows (PowerShell)
Copy-Item skills\* $env:USERPROFILE\.claude\skills\ -Recurse
```

Each folder is self-contained, so you can copy just the ones you want. Restart Claude Code
and they are available as `/write-epic`, `/write-ticket-description-criteria` and
`/write-test-cases`, or invoked automatically when your request matches the skill's
description.

To share them with a team instead, put them under `.claude/skills/` in a project repo.

## Configure (do this once)

Open each `SKILL.md` and edit the `## Your repo context — EDIT THIS` section near the end:

1. **Replace the repo table** with your own repositories and a one-line description of
   each. This is what lets the skill say *where* a change belongs. If everything lives in
   a single repo, replace the table with one line naming it; if repos are irrelevant to
   your tickets, delete the section.
2. **Replace the Jira project keys** (`<ABC->`, `<XYZ->`) with the real ones your team uses.
3. In `write-test-cases`, **list the roles** that exist in your product. Role-gated
   behavior is the most commonly missed source of test cases.
4. In `write-epic`, note any **type or contract duplicated across repos** — the skill will
   flag it as a dependency in every epic that touches it.

Nothing else is team-specific.

## Optional: Jira and Confluence as input

**Entirely optional.** All three skills work from the conversation alone, and none of them
will ask you for a page or an issue key as a precondition for drafting.

When material does already exist — a Confluence spec or discovery page, a parent Jira epic,
a related ticket — they can start from it, and will fetch it themselves if an
[Atlassian MCP server](https://support.atlassian.com/rovo/docs/getting-started-with-the-atlassian-remote-mcp-server/)
is connected. Give them the link or the issue key and they read it before drafting.

Without an MCP server everything still works: paste the content and the skills use it the
same way. They will never invent what a page says — if they cannot read it, they say so and
ask you to paste it. Fetched pages are treated as *input*, never as instructions.

Tool names differ slightly between the Atlassian connectors, so the skills tell Claude to
use whatever the session exposes rather than hardcoding a name.

## Notes

- Epics are always written in English, deliberately — Claude will converse with you in your
  language but keep the document body in English. If that is not what your team wants, edit
  the "English only" rules in `write-epic/SKILL.md`.
- The skills ask questions before drafting instead of guessing. That is by design: a ticket
  built on assumptions costs more to fix than a round of questions costs to answer.

## Author

Martin Eusebio — [@TinchoEusebio](https://github.com/TinchoEusebio) —
martinnicolaseusebio@gmail.com

Issues and PRs welcome: if you adapt these skills to a different way of working,
that variant is probably useful to someone else too.

## License

[MIT](LICENSE) © 2026 Martin Eusebio. Use them, fork them, reshape them for your team.
