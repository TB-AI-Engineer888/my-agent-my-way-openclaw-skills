# Custom Skills Design

These skills solve recurring tasks in Teresa's current bootcamp workflow while
using only the Google services already connected through Zapier MCP.

## Skill 1: Learning Log

### 1. What does this skill do?

It turns Teresa's rough daily learning bullets into a structured, dated
reflection and saves it as a Google Doc.

### 2. What input does the agent need?

Teresa supplies two or more bullets describing what she learned; she may also
include a date, blockers, or a next experiment. Plain text is enough. The skill
already knows from the five configuration files that Teresa is studying AI
engineering, uses America/Toronto, prefers concise Canadian English, wants
evidence of successful writes, and uses the default dedicated Google account.

If no date is supplied, the skill uses today's date in America/Toronto. It may
infer themes from the bullets, but it must not invent technical achievements or
claim a blocker was resolved.

### 3. What does a good output look like?

The destination is a new Google Doc titled
`Learning Log — YYYY-MM-DD — <short theme>`. Its body contains:

1. a one-sentence summary;
2. “What I learned” bullets;
3. “Why it matters”;
4. “Open questions or blockers”; and
5. “Next step”.

The writing should be direct, practical, and visibly tailored to Teresa's AI
engineering studies. Success means Zapier returns a successful Google Docs
write and Charles confirms the exact title and returned link. A local Markdown
file is not a successful fallback.

## Skill 2: Smart Study Event

### 1. What does this skill do?

It converts Teresa's plain-language study commitment into one complete Google
Calendar event with an accurate duration, useful description, and reminder.

### 2. What input does the agent need?

Teresa provides a topic, date or day, approximate or exact start time, and
duration. She may optionally provide a reminder, location, desired outcome, or
related Google Doc link. The skill already knows her America/Toronto time zone,
default calendar connection, concise style, and privacy rules.

The skill asks one focused question if the date, start time, or duration cannot
be resolved safely. It never guesses attendees. If Teresa asks for a free time
rather than naming one, it checks her calendar before proposing or creating the
event.

### 3. What does a good output look like?

The destination is one detailed Google Calendar event titled
`Study — <specific topic>`. It has explicit start and end times, the
America/Toronto time zone, a short description with the intended outcome and up
to three focus bullets, and Teresa's requested reminder (or a documented
default 15-minute reminder).

Success means the connected Calendar tool returns a successful write and
Charles confirms the title, absolute date, 12-hour time range, time zone, and
returned event link or identifier. It must not create a duplicate after a
partial failure.

## Why these two skills

The learning log preserves what Teresa is already learning in a reusable form;
the study event turns the next action into protected time. Together they form a
small repeatable loop—reflect, then schedule—without adding an API, OAuth flow,
or service. Docs is suited to structured material worth revisiting, while
Calendar is the right destination for a time-bound commitment.
