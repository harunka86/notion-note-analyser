---
name: notion-note-analyser
description: Fetch a Notion note, analyse what was written today (or recently), and create a timestamped analysis sub-page inside that same note. Use this skill whenever the user asks to "analyse" or "reflect on" or "make sense of" something they wrote in a Notion note — especially journal-style pages, idea dumps, or stream-of-consciousness pages. Trigger even if they just say "analyse what I wrote today", "make a note for the analysis", "reflect on my Notion entry", or "write up an analysis of my note". For week-scale reviews ("weekly review", "weekly synthesis", "weekly mind map"), use the separate notion-weekly-synthesis skill instead. Always use this skill for single-entry analysis — do NOT attempt to search Notion, read content, and write an analysis without it.
---

# Notion Note Analyser Skill

Fetch a Notion note, analyse what was written (focusing on today's entries or recent additions), and write a thoughtful analysis as a new sub-page inside the same note.

---

## Step 1 — Find the Note

If the user named the note, search for it:

```
Notion:notion-search — query: <note name>
```

If the user did not name a note, ask: "Which Notion note would you like me to analyse?"

Once found, note the page `id` and `url`.

---

## Step 2 — Fetch the Full Content

```
Notion:notion-fetch — id: <page_id>
```

Read the entire content carefully. Pay attention to:
- **Timestamps** embedded in the text (e.g. `[22:42 11/06/2026]`) — use these to identify today's entries
- **Language shifts** — content in a different language than usual signals unfiltered expression; treat it with extra care
- **Tone markers** — single words, repetition, unfinished sentences, emotional vocabulary
- **What is dated vs. undated** — undated content may be older drafts or evergreen thoughts

If the user said "today", focus on entries with today's date. If no date markers exist, analyse all content unless the user specifies otherwise.

---

## Step 2.5 — Pull the Day's Schedule (Google Calendar)

The diary entry captures how the day *felt*; the calendar captures what actually *happened*. Pulling both means the analysis can ground feelings in context (e.g. "three back-to-back lessons plus a job application deadline" explains a lot) instead of treating the entry as if it happened in a vacuum.

1. Work out the target date (today's date, or whatever date the entry in Step 2 is dated to).
2. Fetch that day's events:
   ```
   Google Calendar:list_events
     startTime: <target date>T00:00:00
     endTime: <target date>T23:59:59
     orderBy: startTime
   ```
3. Skim the results for a plain factual picture of the day: what was on the schedule (lessons, meetings, deadlines, appointments), roughly how full or light the day was, and anything notable (back-to-back blocks, an unusually long gap, an event tied to something emotionally significant like a visa appointment or job interview).
4. This is a factual record, not interpretation yet — save the "what does this mean" for Step 4.
5. If the calendar is empty or unavailable for that date, skip this step's output entirely (see Edge Cases) rather than noting an absence.

---

## Step 3 — Identify the Content Type

Before writing, classify what you're looking at. This determines the analysis style:

| Type | Signals | Analysis Approach |
|---|---|---|
| **Emotional / personal venting** | Feelings language, self-doubt, questions like "why", raw grief or frustration | Empathetic mirror: reflect back what was said, name the patterns, reframe gently without dismissing |
| **Ideation / creative dump** | Fragments, half-formed concepts, random associations | Thematic synthesis: cluster related ideas, identify the strongest threads, suggest what's worth pursuing |
| **Planning / operational** | Tasks, goals, blockers, timelines | Actionable summary: extract commitments, highlight risks, suggest next steps |
| **Mixed** | More than one of the above | Address each type in its own section |

---

## Step 4 — Write the Analysis

### What I Did Today (include whenever Step 2.5 found calendar data)

Open the analysis with a short factual section, before the interpretive content:

**What I Did Today** — a brief, plain-prose rundown of the day's schedule from Step 2.5 (e.g. "Two lessons in the morning, then a block for job applications, and a call with a contact in the evening"). Keep this to 2-4 sentences. No interpretation yet — weave the connection between the day's actual events and what was written into the sections below where it's genuinely relevant (e.g. a rough entry after a five-lesson day reads differently than the same words after a quiet one).

If Step 2.5 found no calendar data for the day, skip this section entirely — do not note its absence.

### Psychological Lens (apply across all types, where it genuinely fits)

Before drafting, think about whether any psychological frameworks, concepts, or named works would sharpen the analysis — not to diagnose, but to offer a precise, illuminating lens.

- Draw from frameworks like: attachment theory, self-determination theory (autonomy/competence/relatedness), Bourdieu's forms of capital, cognitive dissonance, locus of control, growth vs fixed mindset, imposter phenomenon, grief/loss models, boundary-setting theory, identity and belonging frameworks (e.g. elective belonging, third-culture-kid theory), decision fatigue, or specific authors and works (Brené Brown, Esther Perel, Daniel Kahneman, Pema Chödrön, etc.) — whichever genuinely illuminates the pattern in front of you, not a forced fit.
- One well-chosen framework beats three name-dropped ones. If nothing fits naturally, skip this entirely.
- Weave it into the existing sections (typically "What I Notice" or "What This Entry Is") rather than adding a separate "Frameworks" header.
- If the framework or thinker isn't widely known, explain it in a single sentence.
- A framework is a lens for understanding, never a label for the person. Do not use it to imply a diagnosis or clinical conclusion.

### For emotional / personal content

Structure the analysis as:

0. **What I Did Today** (if applicable, see above)
1. **What You Wrote** — a faithful, compassionate summary of the entry (translate if needed, preserving the emotional weight; do not sanitise)
2. **What I Notice** — 3-5 named observations. Each should:
   - Name the pattern or theme clearly
   - Offer a reframe or perspective that doesn't dismiss the feeling
   - Be honest, not soothing for its own sake
   - Ground at least one observation in a psychological framework or named work, when one genuinely fits (see Psychological Lens above)
3. **What This Entry Is** — a one-paragraph characterisation of the emotional state or moment: what kind of hard is this? Is it burnout, grief, frustration, fear, self-comparison? Name it precisely.
4. **One thing worth sitting with** — a single closing thought. Not a solution. Just something true.

### For ideation / creative content

Structure the analysis as:

0. **What I Did Today** (if applicable, see above)
1. **Raw inventory** — list all distinct ideas, even fragments
2. **Clusters** — group related ideas by theme; name each cluster
3. **Strongest signal** — the 1-2 ideas with the most energy or specificity; explain why
4. **Connections to existing work** — if the ideas connect to known projects, note the link
5. **Next action (optional)** — only if obvious; don't force it

### For planning / operational content

Structure the analysis as:

0. **What I Did Today** (if applicable, see above)
1. **Commitments identified** — explicit tasks or intentions
2. **Open questions** — things mentioned but unresolved
3. **Risks or blockers** — anything that could stall progress
4. **Suggested priority order** — a ranked list of what to tackle first

---

## Step 5 — Create the Sub-Page

Create a child page inside the source note:

```
Notion:notion-create-pages
  parent: { page_id: <source_page_id>, type: "page_id" }
  pages: [{
    properties: { title: "Analysis — <today's date, e.g. June 11, 2026>" },
    icon: "🪞",
    content: <full analysis in Notion-flavored Markdown>
  }]
```

Date format for the title: `Analysis — [Month DD, YYYY]` (e.g. "Analysis — June 11, 2026")

Note the returned `url` of the newly created sub-page — you will need it in the next step.

---

## Step 5.5 — Link to Related Entries

Before logging to the database, check whether this entry connects to past ones. This is what turns a pile of isolated diary entries into an actual network of thought over time — every new page should link to what it relates to.

1. Query the Diary entries database for past entries:
   ```
   Notion:notion-query-data-sources
     data: { data_source_urls: ["collection://<your-diary-database-id>"], query: "SELECT * FROM \"collection://<your-diary-database-id>\" ORDER BY Date DESC LIMIT 30" }
   ```
2. Scan the `Summary` field of returned entries for thematic overlap with today's entry — same person, same recurring struggle (e.g. job search burnout, a specific relationship dynamic, a specific project), same psychological pattern, or an explicit callback ("like I said before...", "again with...").
3. Only surface genuine matches. 0-3 related entries is normal; forcing a link when nothing fits is worse than no link.
4. If matches are found, add a **"Related entries"** section at the end of the analysis (before it's created in Step 5's page), listing each match as a link with a one-line note on *why* it's related — not just that it is:
   - `[Analysis — May 14, 2026](url) — same evaluation-aversion pattern, different trigger`
5. If this is the first entry on a theme, skip this section entirely rather than writing "no related entries found."

This section becomes part of the sub-page content created in Step 5, so do this check before calling `notion-create-pages`.

---

## Step 5.6 — Grow the Wiki

Wiki database data source ID: `<your-wiki-database-id>`

The Wiki holds a small number of living pages, one per recurring theme (not per entry, not per day). AI only ever **appends** to these pages or **creates** a new one — never rewrites or deletes existing wiki content. The person always has final say over pruning or editing.

1. Query existing Wiki topics:
   ```
   Notion:notion-query-data-sources
     data: { data_source_urls: ["collection://<your-wiki-database-id>"], query: "SELECT * FROM \"collection://<your-wiki-database-id>\"" }
   ```
2. Check whether today's entry's theme matches an existing Topic (reuse the thematic matching from Step 5.5 — same person, same recurring struggle or pattern, explicit callback).
3. **If it matches an existing Topic:**
   - Fetch the current wiki page content.
   - Append a new dated section at the end (e.g. `### Update — July 4, 2026`) synthesizing what's new or changed about the theme, in your own words, not a copy-paste of the diary entry. Link back to today's analysis sub-page.
   - Use `notion-update-page` with `insert_content` (position: end) so existing content is untouched.
   - Update the `Last Updated` property to today's date via `update_properties`.
4. **If no Topic matches, but Step 5.5 found at least one related past entry** (i.e. this is the second time this theme has surfaced): create a new Wiki page now, seeded from both the past entry and today's — this is the point a theme graduates from "mentioned twice" to "worth tracking."
   ```
   Notion:notion-create-pages
     parent: { data_source_id: "<your-wiki-database-id>", type: "data_source_id" }
     pages: [{
       properties: { "Topic": "<theme name>", "date:Last Updated:start": "<today, ISO-8601>", "Status": "active", "Summary": "<1-2 sentence standing summary of the theme>" },
       content: "<synthesized understanding of the theme drawn from both entries, ending with a dated '### Update' section for today, and links to both source analysis sub-pages>"
     }]
   ```
5. **If this is a genuinely first-time, isolated theme:** skip wiki creation entirely. Single occurrences don't get a page — that's what Step 5.5's "no related entries" case already covers.
6. Naming topics: keep them stable and reusable (e.g. "Job search identity", "Elective belonging") so future entries can actually match against them — avoid overly specific one-off titles.

---

## Step 6 — Log to Diary Entries Database

After the sub-page is created, also add an entry to the **Diary entries** database.

Database data source ID: `<your-diary-database-id>`

```
Notion:notion-create-pages
  parent: { data_source_id: "<your-diary-database-id>", type: "data_source_id" }
  pages: [{
    properties: {
      "Name": "Analysis — <today's date, e.g. June 11, 2026>",
      "date:Date:start": "<today's date in ISO-8601, e.g. 2026-06-11>",
      "date:Date:is_datetime": 0,
      "Summary": "<1-2 sentence plain-text summary of the analysis — what content type it was, what the central theme or pattern was>"
    },
    content: "<Link to the full analysis sub-page: [View full analysis](<sub-page url from Step 5>)>"
  }]
```

**Summary field guidance:**
- Keep it to 1-2 sentences, max ~200 characters
- State the content type and the core theme — e.g. "Emotional entry about job search burnout and evaluation aversion. Key pattern: credential-career gap and comparison with others."
- Do not reproduce the full analysis; the Summary is for quick scanning in the database view
- If Step 2.5 found calendar data, you may fold a brief factual note into the Summary when it adds real context (e.g. "...after a five-lesson teaching day"), but don't force it in if the Summary is already tight

---

## Tone Guidelines

- **Do not be clinical.** This is not a therapy session summary.
- **Do not be sycophantic.** Avoid "what a rich entry!" type openers.
- **Do not offer unsolicited solutions** for emotional entries. The analysis is a mirror, not a fix.
- **Frameworks are lenses, not labels.** When you bring in a psychological framework or thinker, use it to illuminate the pattern, never to diagnose or pathologise.
- **Match the register.** If the user wrote informally, write informally. If they wrote with precision, match it.
- **Language notes:** If the entry is in Japanese (or another language), quote key phrases in the original and translate them. Acknowledge that writing in a non-primary language often signals something worth noting — it may indicate unfiltered, private expression.
- **No em-dashes, no en-dashes.** Use hyphens.

---

## After Creating Both Pages

Report back in chat:
- Confirm the analysis sub-page was created inside the source note, and share its Notion link
- Confirm the diary entry was logged to the Diary entries database
- If a Wiki page was created or updated, say so and share its link (one line — e.g. "Also updated the 'Elective belonging' wiki page with today's entry")
- Give a brief (2-3 sentence) verbal summary of what you found — what the content type was, what the central theme was
- Do NOT reproduce the full analysis in chat; it lives in Notion

---

## Edge Cases

- **No entries today**: Tell the user no dated entries for today were found, and ask if they'd like to analyse all undated content instead.
- **Very short entry** (1-3 lines): Analyse it anyway. Brevity is itself meaningful. A short emotional fragment deserves the same care as a long one.
- **Multiple languages in one entry**: Treat each language section faithfully. Note the language shift explicitly in the analysis.
- **Page not found**: Ask the user to check the note name or share the URL directly.
- **Calendar empty or unavailable**: If Google Calendar has no events for the target date, or the tool call fails, just skip the "What I Did Today" section and proceed with the rest of the analysis as normal. Don't mention the calendar at all in this case — an empty day isn't noteworthy, and a failed fetch isn't the user's problem to hear about.
- **Undated entry with no clear target date**: If Step 1-2 couldn't establish a specific date for the content being analysed (e.g. analysing "all content" with no date markers), skip Step 2.5 entirely — there's no single day to pull a schedule for.
