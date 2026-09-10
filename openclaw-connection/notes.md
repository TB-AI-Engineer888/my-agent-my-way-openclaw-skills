Native OpenClaw MCP (streamable HTTP + OAuth) is the integration path, not mcporter. mcporter OAuth cannot finish when the browser is on a laptop and the listener is on the VPS.
Telegram stays in polling. Do not restore the truncated-token webhook experiment from earlier troubleshooting.
Google Docs and Calendar were connected through Zapier MCP with a dedicated Gmail account, and those connections were set as Zapier defaults.
DeepSeek kept calling Zapier names through shell exec. A temporary tools.allow=["bundle-mcp"] plus a gateway restart made the live Telegram runtime use MCP tools; the coding profile was restored after the writes succeeded.
The Google Doc and Calendar event were created with zapier__execute_zapier_write_action. Telegram confirmation was sent only after those writes returned success.
Do not put tokens, connection IDs, or account emails in screenshots or notes.
Pending operator screenshots: OpenClaw channel/MCP status, calendar event, and the Telegram clarifying-question turn.
