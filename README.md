# Teresa's OpenClaw agent

Course projects for the 4Geeks AI Engineering Bootcamp. The live agent runs on
an Ubuntu VPS with OpenClaw 2026.7.1-2.

The earlier connection project remains documented in `openclaw-connection/`.
Telegram, Zapier MCP, Google Docs, and Google Calendar were already connected;
this project does not recreate those integrations.

## Personal-agent project

`My Agent, My Way: Teaching Your Personal Assistant New Skills` adds:

- five specific briefing files in `.openclaw/`;
- the independently committed design in `SKILLS_DESIGN.md`;
- `skills/learning-log`, which creates a structured Google Doc; and
- `skills/smart-study-event`, which creates a complete Calendar event.

Both skills follow the official OpenClaw workspace-skill format and use only
the existing Zapier MCP connections.

## Verify on the VPS

```bash
openclaw doctor
openclaw skills list
openclaw skills info learning-log
openclaw skills info smart-study-event
openclaw mcp doctor zapier --probe
openclaw channels status
```

Do not use the old mcporter registry as the health signal. The working
integration is the native OpenClaw-managed Zapier MCP server.

## Evidence

See `TEST_EVIDENCE.md` for the personal inputs, connected-service results,
read-back verification, and the timezone defect found and corrected during
testing. It excludes account addresses, credentials, connection IDs, and Google
object IDs.

## Submission packet

See `openclaw-connection/` for screenshots and `notes.md`.
