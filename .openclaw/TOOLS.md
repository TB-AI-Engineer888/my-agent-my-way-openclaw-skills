# Connected tools

Use the existing OpenClaw-managed Zapier MCP server. Do not use the stale
mcporter registry and do not start a new OAuth flow.

Use every tool with senior engineering discipline: inspect before changing
when inspection can prevent a mistake, verify connected-service writes before
claiming success, and avoid duplicate external actions. If a multi-step
operation partially fails, preserve successful work, identify the exact failed
step, and report what remains. A tool call alone is not evidence of success.

## Defaults

- **Time zone:** America/Toronto
- **Calendar:** the existing default Google Calendar connection for Teresa's
  dedicated course account
- **Docs/Drive:** the existing default Google Docs/Drive connection; use the
  root location unless Teresa names a folder
- **Gmail sign-off:** `Best,` followed by `Teresa`
- **Confirmation channel:** reply in the current conversation; use Telegram
  delivery only when Teresa asks for it
- Never display account emails, OAuth data, tokens, or Zapier connection IDs.

## Google Docs

Use for learning logs, polished notes, plans, and content Teresa needs to
revisit or share. Give documents descriptive titles with an ISO date when they
are part of a series. Before creating a likely duplicate, search by exact title
when a read action is available.

The known create route is the Zapier write meta-tool with:

- API: `GoogleDocsV2CLIAPI`
- action: `newtxtdocument`
- tool: `google_docs_create_document_from_text`

Inspect the action schema before writing if required fields are uncertain.
Include the returned document URL in the confirmation when available.

## Google Calendar

Use for commitments with a real start and end time. Check the schedule first
when Teresa asks for a free slot. If she supplies an exact time, create it
without a conflict search unless she asks for one. Default to
America/Toronto, but repeat the resolved date, time, duration, and time zone
before writing when relative language was used.

Use the detailed-event action rather than quick add when reminders, duration,
description, or location matter:

- API: `GoogleCalendarCLIAPI`
- action: `detailed_event`
- tool: `google_calendar_create_detailed_event`

Never invent attendees. A reminder request belongs in the event's reminder
fields, not only in its description.

## Gmail

Use for reading or drafting email when requested. Draft first and show Teresa
the recipient, subject, and body. Sending always requires explicit confirmation
in the current conversation. Sign drafts with:

```text
Best,
Teresa
```

## Google Drive

Use to find and organize Teresa's files. Search before concluding a file does
not exist. Moving, renaming, sharing, or deleting requires confirmation.

## Google Tasks

Use for actionable work that has an owner or next step but no fixed time.
Include a concrete verb and due date when Teresa provides one. Do not turn every
informational note into a task.

## GitHub

Use read actions to inspect repositories, issues, commits, and pull requests.
Do not open, edit, close, merge, or comment on anything unless Teresa explicitly
asks for that external change.

## Telegram

Use for concise confirmations and digests, not long documents. Include safe,
useful links, but never credentials or private account identifiers. Sending to
any person or channel other than Teresa requires explicit destination
confirmation.

### Voice replies

OpenClaw native Auto-TTS is configured for every Telegram text-reply block
using the bundled Microsoft provider. Every user-visible text block must have
a complete, matching spoken version delivered to the same conversation. This
is an accessibility requirement: do not summarize, shorten, or omit text from
the spoken version. Long answers are divided by the runtime into blocks that
fit the native TTS limit, with each block delivered as text and voice.

The runtime handles automatic speech. Do not add TTS or audio directives to
normal responses and do not rewrite text for a separate “voice version.” This
path requires no new API key, OAuth flow, account, or external integration.

Auto-TTS skips replies shorter than 10 characters and replies that already
contain structured media. Keep substantive Telegram replies long enough for
speech. Use `/tts status`, `/tts on`, `/tts off`, or `/tts latest` when Teresa
asks to inspect or control voice delivery.

If speech synthesis fails, preserve the text response and report the audio
failure accurately rather than claiming that both formats were delivered.
