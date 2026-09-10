# OpenClaw School Project — Working Log

Course: 4Geeks AI Engineering Bootcamp — Basic Personal Assistants with OpenClaw
Project: Connect Your Agent — Telegram, Google Drive & Calendar
Target: Ubuntu 22.04.5 VPS at 165.227.203.137 (hostname bc-vps-326), SSH as root
OpenClaw: 2026.7.1-2, systemd unit `openclaw-gateway.service`, port 18789

This log is a repeatable record: exact commands, config before/after, decisions, failures, and docs consulted. It is the source for `openclaw-connection/notes.md`.

---

## 0. Local workspace (Cloud Agent)

Commands run:

```bash
git symbolic-ref --short HEAD
git status
ls -la /workspace
ls -la ~/.ssh
```

Results:

- Branch: `main`, clean empty repo (only `.git`)
- No SSH keys on this machine (`~/.ssh` does not exist)
- Egress from this environment is not restricted (Cursor Cloud `environment-info`)

Decision: cannot inspect the VPS until a password (or key) is provided. Do not change anything on the VPS until First Actions 1–5 are complete.

Docs consulted this step: none yet (waiting on SSH).

---

## 1. Connect to the VPS

Required from the operator (not available in this environment):

- SSH as `root` to `165.227.203.137`
- Password for that account

Password received from the operator. Stored only in the SSH session environment (`SSHPASS`); not written to this repo.

Local helper (this machine, not the VPS):

```bash
sudo apt-get update -qq && sudo apt-get install -y -qq sshpass
```

Connect (do not log the password):

```bash
export SSHPASS='<VPS_ROOT_PASSWORD>'
sshpass -e ssh -o StrictHostKeyChecking=accept-new -o UserKnownHostsFile=/tmp/vps_known_hosts root@165.227.203.137
```

Result: connected. Hostname `bc-vps-326`, kernel `5.15.0-190-generic`, Ubuntu-family, up 16 days. No files changed on the VPS in this step.

---

## 2. Inspect (read-only)

Commands run over SSH (exact):

```bash
hostname
uname -a
date
uptime
systemctl status openclaw-gateway.service --no-pager -l
systemctl is-enabled openclaw-gateway.service
ss -tlnp
ps aux | grep -E "openclaw|mcporter|node|python|zapier" | grep -v grep
ls -la /root/.openclaw/
ls -la /root/.openclaw/workspace/
ls -la /root/.openclaw/workspace/config/
which openclaw mcporter node npm npx
openclaw --version
node --version
systemctl list-units --all --no-pager | grep -iE "openclaw|claw|gateway|mcp"
ls /etc/systemd/system/ | grep -iE "openclaw|claw|gateway"
cat /root/.openclaw/openclaw.json
cat /root/.openclaw/openclaw.json.backup
cat /root/.openclaw/openclaw.json.last-good
cat /root/.openclaw/workspace/config/mcporter.json
ps -fp 326546 -o pid,ppid,lstart,cmd
ps -fp 699 -o pid,ppid,user,lstart,cmd
crontab -l
systemctl list-units --type=service --state=running --no-pager
ls -la /root/.openclaw/credentials/
ls -la /root/.openclaw/logs/
ls -la /root/.openclaw/state/
find /usr /root /opt /home -name "*mcporter*"
npm list -g --depth=0
openclaw --help
openclaw daemon status
openclaw health
openclaw status
openclaw channels status
openclaw mcp --help
openclaw mcp list
openclaw skills list
openclaw plugins list
ls -la /root/.mcporter/
cat /root/.mcporter/credentials.json
grep -n -iE "mcp|zapier|mcporter|telegram|google|npx|daemon|gateway" /root/.bash_history
tail /root/.openclaw/logs/config-audit.jsonl
cat /root/.openclaw/credentials/telegram-default-allowFrom.json
cat /root/.openclaw/credentials/telegram-pairing.json
cat /root/.config/systemd/user/openclaw-gateway.service
python3 -c 'import json; print(json.load(open("/root/.npm/_npx/bdbf2deecdd22bc5/node_modules/mcporter/package.json")).get("version"))'
head -80 /usr/lib/node_modules/openclaw/skills/mcporter/SKILL.md
cat /root/.openclaw/agents/main/sessions/sessions.json
find /root/.openclaw/agents -name "*.jsonl"
grep -iE "zapier|mcporter|mcp|oauth|callback" /tmp/openclaw/openclaw-2026-09-10.log /tmp/openclaw/openclaw-2026-09-09.log
journalctl --user -u openclaw-gateway.service -n 80 --no-pager
# Telegram Bot API getMe / getWebhookInfo using the token already in openclaw.json
openclaw mcp add --help
openclaw mcp set --help
openclaw mcp login --help
python3  # extract Telegram session 86edf8aa-...jsonl roles/text
cd /root/.openclaw/workspace && npx -y mcporter@0.13.10 --help
cd /root/.openclaw/workspace && npx -y mcporter@0.13.10 config list
cd /root/.openclaw/workspace && npx -y mcporter@0.13.10 list zapier --schema
```

Docs consulted:

- `openclaw --help`, `openclaw mcp --help`, `openclaw mcp add --help`, `openclaw mcp login --help`
- `/usr/lib/node_modules/openclaw/skills/mcporter/SKILL.md`
- https://docs.openclaw.ai/cli/mcp
- https://docs.zapier.com/mcp/get-started/connect/openclaw
- https://docs.zapier.com/mcp/overview/how-connections-work
- https://docs.zapier.com/mcp/get-started/authentication
- https://zapier.com/blog/automate-openclaw-zapier-mcp/

No VPS files, services, or configs were modified.

### What the system actually is

- Gateway is running: pid 326546, `/usr/bin/node ... gateway --port 18789`, listening on `127.0.0.1:18789` and `[::1]:18789` only.
- Unit is a **user** systemd service: `~/.config/systemd/user/openclaw-gateway.service`, parent pid 699 = `/lib/systemd/systemd --user`.
- `systemctl status openclaw-gateway.service` (system bus) fails: unit not found. Brief implied a system unit; that is wrong. Use `systemctl --user` or `openclaw daemon status`.
- `journalctl --user -u openclaw-gateway.service` has no journal files. File logs are `/tmp/openclaw/openclaw-YYYY-MM-DD.log`.
- OpenClaw 2026.7.1-2, Node v26.7.0. mcporter 0.13.10 present via npx cache, not on PATH as `/usr/bin/mcporter`.
- Telegram in `openclaw.json`: `channels.telegram.enabled=true`, full bot token present. `openclaw channels status`: enabled, configured, running, connected, **polling** (webhook URL empty). Pairing allowFrom is Telegram user `8916402767`. `getMe`: bot username `Charles808bot`, id 8789979042.
- Session `agent:main:telegram:direct:8916402767` last activity ~3 days ago (2026-09-06). Transcript shows user "Hello" → agent "Hello! How can I help you today?" then a long Zapier MCP setup conversation.
- Model: primary `litellm/downtown-miami/openrouter/deepseek/deepseek-v4-flash`, fallback `litellm/claude-opus-4-6`, LiteLLM `https://llm.4geeks.ai`. Matches the brief.
- `skills.entries.mcporter.enabled` is **false**. Native `mcp.servers` in `openclaw.json` is **absent**. `openclaw mcp list`: "No OpenClaw-managed MCP servers configured".
- `/root/.openclaw/workspace/config/mcporter.json` contains zapier → `https://mcp.zapier.com/api/v1/connect`, clientName `openclaw`.
- `/root/.mcporter/credentials.json`: OAuth **in progress** (client_id, codeVerifier, state, redirect `http://127.0.0.1:44531/callback`). **No access_token / refresh_token.**
- `npx mcporter list zapier --schema` → 401, "run `mcporter auth zapier` to finish authentication."

### Leftover incorrect troubleshooting (do not restore)

- Backup `openclaw.json.backup` has a truncated Telegram token (secret suffix only, missing `botId:` prefix). History shows `openclaw config set channels.telegram.botToken` with that truncated value, later fixed with `openclaw channels add --token "<full token>"`.
- History shows failed webhook registration against `https://165.227.203.137[:port]/hooks` using the truncated token. Current mode is polling; webhook URL is empty. Leave it.

---

## 3. Task status from the system (not from the brief)

| Task | Actual status | Evidence |
|------|---------------|----------|
| 1 Telegram bot + token | **Complete** | `getMe` ok, username `Charles808bot` (brief wrote `@Charles808Bot`; Telegram usernames are case-insensitive) |
| 2 OpenClaw Telegram channel | **Complete** | `channels.telegram.enabled=true`, channels status connected, polling |
| 3 Test message / agent replies | **Complete** | Transcript 2026-09-06: user Hello → agent replies; later multi-turn Zapier conversation |
| 4 Zapier account | **Complete enough to proceed** | User completed Zapier login/authorize in browser during the 2026-09-06 session. Cannot see the Zapier dashboard from this VPS. |
| 5 MCP endpoint URL | **URL is the real Zapier endpoint; not a unique per-server URL** | Zapier docs: every client uses `https://mcp.zapier.com/api/v1/connect`. Brief's "OpenClaw MCP Server" name matches Zapier's OAuth auto-provisioning. |
| 6 Add MCP to OpenClaw | **Incomplete** | Registration command succeeded (`mcporter config add`). Auth did not. Agent still gets HTTP 401. Native `mcp.servers` empty. |
| 7–17 | Not started | No Google account; no Docs/Calendar tools; last Telegram activity is the failed Zapier OAuth, not a document request |

---

## 4. Why Task 6 did not finish

Telegram transcript (session `86edf8aa-8c60-463a-977b-62ed69836ab7`, 2026-09-06) matches Zapier's official OpenClaw prompt: install mcporter, `mcporter config add zapier --url https://mcp.zapier.com/api/v1/connect`, then `mcporter auth`.

What happened:

1. `mcporter config add` wrote `config/mcporter.json`. That is why "the MCP registration command already ran successfully."
2. `mcporter list zapier` returned 401, as expected before OAuth.
3. `mcporter auth zapier` started a loopback OAuth listener on the **VPS**, first at `http://127.0.0.1:33211/callback` (later random ports 44061, 44969, 36259, 44531).
4. The operator browses from a Windows laptop / phone, not from the VPS. Zapier approved the request. The browser then loaded `http://127.0.0.1:33211/callback?code=...` on **the laptop**, not on the VPS.
5. The VPS listener timed out / was killed by the agent process watchdog before a valid callback arrived.
6. The operator pasted the laptop callback URL into Telegram. The agent forwarded the authorization `code` to a **new** listener on a different port. Zapier HTML said "Authorization successful", but PKCE `code_verifier` did not match the new session, so no tokens were stored.
7. Current credentials file still has client metadata + codeVerifier and **no tokens**. Live probe is still 401.

This is not a Zapier-account problem and not a wrong endpoint. It is a loopback OAuth callback that cannot complete when the browser is not on the machine running `mcporter`.

Zapier docs (how-connections-work): two auth paths — (A) OAuth from inside the client, (B) a dashboard **connection token** (`Authorization: Bearer`) to the same URL, for clients / headless setups that cannot finish OAuth. Course material accepts the dashboard route.

OpenClaw docs (`openclaw mcp`): native `mcp.servers` in `openclaw.json` is what the agent runtime uses. `mcporter.json` is a separate registry. The mcporter **skill** is disabled, so even a completed mcporter OAuth would not automatically expose tools to Telegram unless the agent shells out to `npx mcporter`.

---

## 5. Decision — next work (not done yet)

Do **not** retry `mcporter auth` OAuth against 127.0.0.1 on the VPS. That is the path that already failed.

Intended fix for Task 6 (simplest that matches vendor docs + the headless VPS):

1. Operator generates a Zapier MCP **connection token** in the Zapier MCP dashboard (Connect tab) for the existing server. That is on the operator's list (browser login).
2. On the VPS, add Zapier as an OpenClaw-managed MCP server with Streamable HTTP and the Bearer header, then probe:

```bash
openclaw mcp add zapier \
  --url "https://mcp.zapier.com/api/v1/connect" \
  --transport streamable-http \
  --header "Authorization: Bearer <CONNECTION_TOKEN>"
openclaw mcp doctor zapier --probe
```

3. Confirm `openclaw mcp list` shows zapier and tools are listed. Do not enable extra skills unless a probe shows they are required.
4. Leave Telegram alone. Do not recreate the bot.

Then stop and prompt for a dedicated Gmail before Tasks 7–8.

No config changes until this report is delivered.

---

## 6. Task 6 repair — native OpenClaw OAuth

Operator reported that the existing OpenClaw server's Connect tab opens an app-account dialog (for example Google Docs) and has no Generate token button.

Decision correction: Zapier's token UI is only for an MCP server created with client type **Other** (unlisted client). The existing server follows Zapier's listed OpenClaw OAuth path, so it correctly does not expose a connection token. Do not create a second server merely to obtain a token.

OpenClaw's current documentation provides the missing remote-host mechanism:

> For a loopback redirect, OpenClaw listens for the browser callback and completes login automatically. The printed `--code` command remains the fallback for remote, headless, or unreachable callbacks.

Source: https://docs.openclaw.ai/cli/mcp, OAuth workflow / manual fallback.

Configuration change:

- Before: `/root/.openclaw/openclaw.json` had no `mcp` object / no native `mcp.servers`.
- Backup created: `/root/.openclaw/openclaw.json.pre-native-mcp-20260910T0336Z`
- After: native `mcp.servers.zapier` is:

```json
{
  "url": "https://mcp.zapier.com/api/v1/connect",
  "transport": "streamable-http",
  "auth": "oauth"
}
```

Exact commands:

```bash
cp -a /root/.openclaw/openclaw.json /root/.openclaw/openclaw.json.pre-native-mcp-20260910T0336Z
openclaw mcp add zapier --url "https://mcp.zapier.com/api/v1/connect" --transport streamable-http --auth oauth --no-probe
openclaw mcp show zapier
openclaw config validate
```

Results:

- `Saved MCP server "zapier" to /root/.openclaw/openclaw.json.`
- Native MCP object displays exactly as above.
- `Config valid: ~/.openclaw/openclaw.json`

Next: start `openclaw mcp login zapier`, give the operator the new authorization URL, keep the same OAuth request alive, then redeem the returned code with `openclaw mcp login zapier --code '<code>'`. Do not use the stale mcporter callback/code.

OAuth request started:

```bash
openclaw mcp login zapier
```

Result:

- OpenClaw registered a native OAuth client and printed a Zapier authorization URL.
- Redirect for this request is `http://127.0.0.1:8989/oauth/callback`.
- OpenClaw explicitly printed: `After approval, run openclaw mcp login zapier --code <code>.`
- This confirms the manual-code fallback is active; no SSH tunnel is needed.
- The authorization URL is intentionally omitted from this durable log because it is a one-time request containing state and PKCE parameters.

Waiting for the operator to open the new URL, approve it, and return the resulting localhost callback URL (or its `code` value). Do not start another login request while waiting; it would replace the matching PKCE state.

Operator returned the callback with matching state. The one-time code is omitted from this log.

Exact commands (secret redacted):

```bash
openclaw mcp login zapier --code "<ONE_TIME_AUTHORIZATION_CODE>"
openclaw mcp status --verbose
openclaw mcp doctor zapier --probe
openclaw mcp probe zapier --json
```

Results:

- `MCP OAuth credentials saved for "zapier".`
- Status: `zapier: streamable-http oauth authorized`
- OAuth: `tokens=yes client=yes`
- Doctor: `zapier: ok`
- Probe: 17 tools, no diagnostics.
- Available tools include connection management, action discovery/inspection, action enable/disable, and Zapier read/write action execution.

Decision: **Task 6 is complete.** Native OpenClaw MCP is authenticated and active. The stale mcporter registry remains unauthenticated but is no longer the integration path; do not retry it or treat its 401 as the project status.

Stop here before Task 7. Per project instructions, the operator must create a new dedicated Google/Gmail account (not a personal account) and confirm it is ready before Google Docs or Google Calendar connections are started.

---

## 7. Google account confirmation and Zapier app setup

Operator confirmed the dedicated Gmail account is created, can sign in, and is ready for Google consent.

Decision: drive setup through the OpenClaw agent and its authenticated native Zapier MCP tools. Start in a fresh setup session so old Telegram OAuth troubleshooting context does not pollute tool selection. Inspect current connections first; do not create the rubric document/event yet.

Exact command:

```bash
openclaw agent \
  --session-key agent:main:zapier-google-setup \
  --message "Use the authenticated native Zapier MCP tools. First inspect existing Zapier app connections and retrieve the Zapier onboarding guidance. We need a dedicated test Google account connected for exactly these rubric capabilities: Google Docs create-document and Google Calendar create-event. Do not create a document or event yet. Initiate any required connection/setup process. If browser authorization is required, return the exact configuration or authorization URL(s) and concise instructions for the human. Use MCP tools and report actual results; do not assume connections exist." \
  --json \
  --timeout 600
```

Results (10 MCP calls, no failures):

- Before: no Zapier actions enabled and no Google app connections.
- Retrieved `zapier:onboarding`.
- Discovered:
  - Google Docs API: `GoogleDocsV2CLIAPI`
  - Google Calendar API: `GoogleCalendarCLIAPI`
- Enabled Google Docs: 15 actions. Rubric create-document capability is `newtxtdocument`.
- Enabled Google Calendar: 14 actions. Rubric create-event capabilities include `detailed_event` and `event`.
- After enabling: both apps report `needs_auth: true`; neither has a connected Google account yet.
- Generated account-authorization URLs:
  - Google Docs: `https://mcp.zapier.com/api/v1/connect-auth/GoogleDocsV2CLIAPI?accountId=28599775`
  - Google Calendar: `https://mcp.zapier.com/api/v1/connect-auth/GoogleCalendarCLIAPI?accountId=28599775`

Decision: stop for the operator's browser consent. Both URLs must be opened with the same new dedicated Google account. After confirmation, query `list_zapier_connections` for both APIs and inspect enabled actions to prove Tasks 7–8.

Operator confirmed both Google consents.

Exact command:

```bash
openclaw agent \
  --session-key agent:main:zapier-google-setup \
  --message "The human confirmed both Google Docs and Google Calendar are connected. Verify from Zapier MCP tools only..." \
  --json \
  --timeout 600
```

Results (emails redacted):

- Google Docs: 1 active non-stale connection. Write action present: `newtxtdocument` / `google_docs_create_document_from_text`.
- Google Calendar: 1 active non-stale connection. Write actions present: `detailed_event` / `google_calendar_create_detailed_event` and `event` / `google_calendar_quick_add_event`.
- No document or event was created in this verification step.

Decision: **Tasks 7 and 8 are complete.**

Configuration change to make the Telegram end-to-end pass:

- Before: `/root/.openclaw/workspace/TOOLS.md` had only greeting/example notes.
- Backup: `/root/.openclaw/workspace/TOOLS.md.pre-zapier-20260910T0406Z`
- After: added a `## Zapier Google tools` section. If a document request is missing title, body, or review time, ask first; then create the Doc plus a review Calendar event; confirm in Telegram; never print emails, names, or tokens.

Reason: items 10 and 23 require a real clarifying question. Items 11–13 require a document, a review event, and a Telegram completion message.

Stop for the operator to send the underspecified Telegram request from the phone. Do not send it from the VPS.

---

## 8. First end-to-end Telegram attempt — failed before external writes

Operator sent:

```text
Create a document about OpenClaw homework
```

Agent correctly asked for title, content, and review time. Operator answered with title `OpenClaw homework notes`, short outline, and a 30-minute review tomorrow at 3pm; agent asked for timezone; operator answered Toronto.

Observed failure (Telegram transcript `/root/.openclaw/agents/main/sessions/b52c656d-a84b-4eb4-81e8-952a5eb4388f.jsonl`):

- The Telegram session received only the literal MCP meta-tools (`zapier__execute_zapier_write_action`, etc.), not app actions as top-level tools.
- The agent incorrectly tried top-level tool `newtxtdocument` three times. Each returned exactly: `Tool newtxtdocument not found`.
- It attempted disabled `web_search` twice and searched local docs/CLI repeatedly for Zapier tool names.
- It never called `zapier__execute_zapier_write_action`.
- It wrote `/root/.openclaw/workspace/openclaw-homework-notes.md` locally (3213 bytes); this is not a Google Doc.
- Session ended `failed`; Telegram displayed: `Agent couldn't generate a response. Some tool actions may have already been executed.`

Conclusion: the warning is generic. Transcript evidence says no Zapier write request was issued, so neither Google object could have been created by this attempt. Nevertheless, perform read-only searches through Zapier before retrying.

Screenshot received from operator: original phone screenshot stored as `assets/76b362d6-450c-48cc-a2b8-098d6c283280.png`; it contains the failed conversation and is not final submission evidence.

Local repo staging attempt failed (no VPS impact):

```bash
git add work-log.md assets/76b362d6-450c-48cc-a2b8-098d6c283280.png
```

Reason: the uploaded image is held in Cursor's project asset store outside `/workspace`, so that relative repo path does not exist. Commit the log only; copy evidence into the submission folder during final assembly.

Read-only verification command:

```bash
openclaw agent \
  --session-key agent:main:zapier-artifact-check \
  --message "Use only the native Zapier MCP meta-tools. Read-only verification; do not call execute_zapier_write_action ... search Google Docs for OpenClaw homework notes and Google Calendar for 2026-09-11 around 15:00 America/Toronto ..." \
  --json \
  --timeout 600
```

Verification results:

- Google Docs exact title search: zero results.
- Google Calendar searches around 3pm Toronto, across the full day, and with no search term: zero results.
- Conclusion confirmed: neither external object exists. Safe to retry once.

Additional defect found during read-only calls:

- Before: both Google app accounts were connected, active, and non-stale, but neither was selected as the Zapier default. An action without `connection_id` failed: `No default connection is set for Google Calendar`.
- Change: called `zapier__manage_zapier_connections` for Google Docs and Google Calendar with their existing connection IDs as `default_connection_id`.
- After: both APIs report one default connection. Connection IDs and email are intentionally omitted from this repo log.
- Reason: Telegram actions should use the dedicated test account without exposing or requiring account identifiers in the prompt.

Agent-instruction correction:

- Before: TOOLS.md named action keys `newtxtdocument`, `detailed_event`, and `event` but did not explain that they are not top-level tools.
- After: TOOLS.md says to call `zapier__execute_zapier_write_action` with:
  - Docs: `selected_api=GoogleDocsV2CLIAPI`, `action=newtxtdocument`, `tool_name=google_docs_create_document_from_text`, required `title` + `file`.
  - Calendar: `selected_api=GoogleCalendarCLIAPI`, `action=detailed_event`, `tool_name=google_calendar_create_detailed_event`, after schema inspection.
- It explicitly forbids direct `newtxtdocument`/`detailed_event` calls, web/shell searching for Zapier tools, or using a local file as a Google Doc substitute.

Exact VPS edit command:

```bash
python3  # replace the `## Zapier Google tools` section in /root/.openclaw/workspace/TOOLS.md
```

Decision: preserve the failed Telegram conversation because it proves the required clarification. Ask the operator to send one precise continuation message from the phone; the next turn will load corrected workspace instructions and must use the MCP write meta-tool directly.

---

## 9. Second Telegram attempt — MCP tools absent from channel session

Operator screenshot showed repeated assistant messages and an exit-127 shell error. Transcript inspection confirmed:

- The model tried `zapier__inspect_zapier_actions` via `exec`, producing `/bin/sh: 1: zapier__inspect_zapier_actions: not found`.
- Direct tool attempts also returned `Tool zapier__inspect_zapier_actions not found`.
- This was not merely a model routing mistake: `systemPromptReport.tools.entries` for the Telegram session contained **zero** `zapier__*` tools.
- In contrast, the CLI setup and verification sessions each contained all 17 `zapier__*` tools.
- The Telegram run was killed after looping; no Zapier write action ran.

Root cause: the Telegram session had cached an empty MCP catalog. Workspace prose cannot make a missing runtime tool callable.

Screenshot received: `assets/6550d6bc-ce22-4ba5-ad87-a5a378a8c612.png` in Cursor's external asset store. It is diagnostic evidence, not final proof.

Session reset:

```bash
cp -a /root/.openclaw/agents/main/sessions/sessions.json /root/.openclaw/agents/main/sessions/sessions.json.pre-telegram-mcp-reset-20260910T0456Z
openclaw gateway call sessions.reset --params '{"key":"agent:main:telegram:direct:8916402767","reason":"repair-mcp-tool-catalog"}' --json
```

First reset failed without changing the session:

```text
invalid sessions.reset params: at /reason: must be equal to constant
```

Source inspection showed allowed reasons are `new` or `reset`. Retried:

```bash
openclaw gateway call sessions.reset --params '{"key":"agent:main:telegram:direct:8916402767","reason":"reset"}' --json
```

Result: success; old transcript archived and new Telegram session ID created while preserving Telegram route metadata.

Non-delivered diagnostic:

```bash
openclaw agent \
  --session-key agent:main:telegram:direct:8916402767 \
  --message "Diagnostic only: do not call any tool and do not deliver externally. Reply with exactly READY." \
  --json \
  --timeout 180
```

Result:

- Run completed; not delivered to Telegram.
- New Telegram session tool catalog: 17 `zapier__*` tools.
- `zapier__execute_zapier_write_action` and `zapier__inspect_zapier_actions` are present.
- Tool schema size increased from 23,637 chars (broken session) to 29,341 chars (fixed session).

Decision: tool routing is now repaired and verified before user retry. Because backend context was reset, send one self-contained request containing title, body scope, exact date/time/timezone, event duration, and direct-MCP instruction.
