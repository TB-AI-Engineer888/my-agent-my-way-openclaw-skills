---
name: smart-study-event
description: Create a complete Google Calendar study event from plain language
user-invocable: true
---

# Smart Study Event

Use this skill when Teresa asks to schedule study, practice, review, or focused
coursework. The required destination is Google Calendar through the existing
Zapier MCP connection.

## Before acting

1. Read `IDENTITY.md`, `SOUL.md`, `AGENTS.md`, `USER.md`, and `TOOLS.md` from
   the active OpenClaw workspace.
2. Extract:
   - a specific study topic;
   - a calendar date;
   - a start time;
   - a duration or end time;
   - an optional intended outcome, location, reminder, or related Doc link.
3. Resolve relative dates in `America/Toronto` and repeat the resulting
   absolute date before the write.
4. If the date, start time, or duration cannot be safely resolved, ask one
   compact question covering only the missing fields. Do not create an event
   yet.
5. Never add attendees unless Teresa explicitly supplies and confirms them.

If Teresa asks for “a free time” or gives only a broad window, inspect Google
Calendar for availability and propose the best matching slot. Ask for approval
before creating that proposed slot. If she gives an exact time, no conflict
check is required unless she requests one.

## Build the event

- **Title:** `Study — <specific topic>`
- **Start/end:** explicit date-times in `America/Toronto`
- **Description:** one sentence stating the intended outcome, followed by up to
  three concise focus bullets derived from Teresa's input
- **Location:** include only if provided
- **Reminder:** use Teresa's requested reminder; if none is supplied, use a
  15-minute reminder and disclose that default before writing
- **Related material:** include a supplied Google Doc link in the description

Use 12-hour time in user-facing text, with `America/Toronto` shown. For
`start__dateTime` and `end__dateTime`, submit a complete RFC 3339 timestamp
including the UTC offset that applies in Toronto on that date (for example,
`2026-09-11T10:00:00-04:00`). Never submit a timezone-less value such as
`2026-09-11T10:00:00`; Zapier may reinterpret it and shift the event.

## Confirm, create, and verify

1. Before writing, give a single-line confirmation containing the title,
   absolute date, time range, time zone, and reminder. If all required details
   came directly from Teresa, this is a transparent pre-write summary, not a
   second approval request.
2. Inspect the detailed Google Calendar action schema with the Zapier MCP
   inspection tool.
3. Call the Zapier MCP write executor—not shell—with:
   - `selected_api`: `GoogleCalendarCLIAPI`
   - `action`: `detailed_event`
   - `tool_name`: `google_calendar_create_detailed_event`
   - the title, start, end, time zone, description, and reminder mapped to the
     schema's fields.
4. Compare the returned event's start and end values with Teresa's requested
   values. Treat the skill as successful only when the write reports success
   and those returned values match.
5. If the returned times differ, report the mismatch and ask before updating
   the created event. Do not create another event.
6. Reply as Charles with the exact title, absolute date, 12-hour time range,
   `America/Toronto`, and returned event link or safe identifier. Keep the
   confirmation concise.

If a tool call fails after an event may have been created, inspect Calendar
before retrying. Never create a second event merely because the first response
was ambiguous.
