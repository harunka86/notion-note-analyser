# notion-note-analyser

A Claude skill that fetches a Notion journal entry, analyses what you wrote, and creates a timestamped analysis as a sub-page inside the same note — turning a private diary into something that reflects back at you instead of just piling up unread.

## What it does

Point it at a Notion page (a journal, an idea dump, a planning note) and it will:

1. Fetch the full page content and figure out what's new (today's entries, or recent additions).
2. Optionally pull that day's Google Calendar events, so the analysis can ground feelings in what actually happened that day (a rough entry reads differently after a five-meeting day than after a quiet one).
3. Classify the content — emotional/personal, ideation/creative, planning/operational, or a mix — and apply a matching analysis structure.
4. Write a considered analysis (what you wrote, patterns noticed, one thing worth sitting with — no unsolicited advice, no clinical tone) and save it as a child sub-page of the original note.
5. Optionally link the new analysis to related past entries and grow a lightweight "wiki" of recurring themes over time, so patterns become visible across weeks and months rather than staying buried in individual entries.

It is deliberately not a therapist or a to-do generator. For emotional entries it acts as a mirror: it names patterns honestly, without dismissing the feeling or rushing to fix it.

## How it's triggered

Use it whenever you want to reflect on or make sense of something you wrote in Notion — phrases like:

- "Analyse what I wrote today"
- "Reflect on my Notion entry"
- "Make sense of this note"
- "Write up an analysis of my journal entry"

For week-scale reviews (a weekly synthesis across several days of entries), use a separate weekly-review skill instead — this one is built for single-entry, point-in-time analysis.

## How to use it

This is a [Claude skill](https://docs.claude.com/en/docs/agents-and-tools/skills) — a markdown file with instructions that Claude follows when the trigger conditions match. To use it:

1. Add `SKILL.md` to your skills directory (for Claude Code, Cowork, or any Claude client that supports skills).
2. Make sure you have Notion connected (via the Notion MCP/connector) so Claude can search, fetch, and create pages.
3. Optionally connect Google Calendar for the "what actually happened that day" context step.
4. Ask Claude to analyse a journal entry, by name or by just saying "analyse what I wrote today."

### Setup notes

The `SKILL.md` in this repo has the database/collection IDs replaced with placeholders (`<your-diary-database-id>`, `<your-wiki-database-id>`). To use this yourself:

- Replace those placeholders with the data source IDs of your own Notion "Diary entries" database and (optionally) a "Wiki" database for recurring themes.
- If you don't want the related-entries linking or wiki-growing steps, you can skip Steps 5.5 and 5.6 entirely — the core fetch-analyse-create-subpage flow (Steps 1-5) works standalone against any single Notion page.

## Why I built this

I journal in Notion, and for a long time entries just accumulated — useful to write, rarely revisited. This skill gives every entry a second pass: something else reading it back and naming what I might not notice myself in the moment. Sharing it here in case it's useful to anyone else who journals and wants a low-effort way to actually engage with what they've written.

## License

Feel free to use, adapt, and share.
