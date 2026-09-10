---
name: learning-log
description: Turn Teresa's learning bullets into a structured, dated Google Doc
user-invocable: true
---

# Learning Log

Use this skill when Teresa asks to capture, record, or save what she learned.
The required destination is Google Docs through the existing Zapier MCP
connection.

## Before acting

1. Read `IDENTITY.md`, `SOUL.md`, `AGENTS.md`, `USER.md`, and `TOOLS.md` from
   the active OpenClaw workspace. Their privacy, voice, date, and tool rules
   apply to every step.
2. Extract Teresa's learning points without adding accomplishments or technical
   claims she did not provide.
3. Require at least two meaningful learning points. If fewer are present, ask
   one question: “What is one more thing you learned or found difficult?”
4. Resolve the entry date in `America/Toronto`. Use a supplied date; otherwise
   use the current date.
5. Derive a short theme of two to five words from the supplied points.

## Compose the document

Use this exact title pattern:

`Learning Log — YYYY-MM-DD — <short theme>`

Write concise Canadian English in Teresa's voice. Use this structure:

```markdown
# Learning Log — Month D, YYYY

## Summary
<One sentence connecting the main ideas.>

## What I learned
- <Clear learning point>
- <Clear learning point>

## Why it matters
<One short paragraph tied to Teresa's AI engineering or personal-assistant work.>

## Open questions or blockers
- <Only questions or blockers supported by the input, or “None noted.”>

## Next step
<One concrete, proportionate follow-up.>
```

Improve structure and grammar, but preserve Teresa's meaning. Avoid generic
encouragement and do not turn guesses into facts.

## Create and verify

1. If a read/search action is available, search Google Docs for the exact title
   to avoid an accidental duplicate. If it exists, stop and ask whether Teresa
   wants another copy; do not overwrite it.
2. If needed, inspect the Google Docs create-action schema with the Zapier MCP
   inspection tool.
3. Call the Zapier MCP write executor—not shell—with:
   - `selected_api`: `GoogleDocsV2CLIAPI`
   - `action`: `newtxtdocument`
   - `tool_name`: `google_docs_create_document_from_text`
   - the composed title and body in the schema's required fields.
4. Treat the skill as successful only when the write tool reports success.
5. Reply as Scout with the exact document title and returned Google Docs link
   when available. Keep the confirmation to two short sentences.

If the tool fails, state that no verified Google Doc was created and report the
specific error. Never substitute a local file.
