# OpenClaw connection project

Course submission for 4Geeks AI Engineering Bootcamp: Connect Your Agent (Telegram, Google Docs, Google Calendar).

This repository is configuration evidence, not an application. The live agent runs on an Ubuntu VPS with OpenClaw 2026.7.1-2.

## What is connected

- Telegram bot as the OpenClaw messaging channel (polling)
- Zapier MCP as an OpenClaw-managed server at `https://mcp.zapier.com/api/v1/connect`
- Google Docs create-document and Google Calendar create-event through that MCP

## Status commands (on the VPS)

```bash
openclaw channels status
openclaw mcp list
openclaw mcp status --verbose
openclaw mcp doctor zapier --probe
openclaw mcp probe zapier
```

## Submission packet

See `openclaw-connection/` for screenshots and `notes.md`.
