# Project Learning Log

## 2026-09-10 — Personalize Charles and add reusable OpenClaw skills

### Problem and result

Teresa needed an existing OpenClaw agent personalized through its five briefing
files and extended with at least two proper custom skills. The finished system
uses Charles's identity and engineering posture, retains the existing Telegram
and native Zapier MCP integrations, and provides verified Learning Log and
Smart Study Event skills.

### Architecture

- `IDENTITY.md`: Charles's name, symbol, and operating roles.
- `SOUL.md`: durable engineering, communication, teaching, career-coaching,
  project-learning, and business-analysis posture.
- `AGENTS.md`: privacy limits, external-action safeguards, engineering
  workflow, project entry points, validation, and reusable-knowledge rules.
- `USER.md`: Teresa's context, goals, current projects, environment, and
  execution/learning preferences.
- `TOOLS.md`: existing service capabilities, identifiers, defaults, and
  connected-write safeguards.
- `skills/<skill-name>/SKILL.md`: user-invocable OpenClaw workspace skills.
- Native OpenClaw Zapier MCP: Google Docs and Calendar read/write execution.
- Telegram: existing conversational channel in polling mode.

### Repeatable implementation pattern

1. Inspect the existing installation, repository, connections, and live
   workspace before changing anything.
2. Run `openclaw doctor` and targeted channel/MCP checks to establish the
   baseline.
3. Write `SKILLS_DESIGN.md` first and commit it before skill implementation
   when the rubric requires design-first history.
4. Keep one custom skill per directory with valid YAML frontmatter and a
   `SKILL.md`.
5. Make each skill explicitly apply the five briefing files.
6. Back up live workspace files before deploying focused replacements.
7. Validate skill discovery with `openclaw skills info <name>`.
8. Test with real input against existing connected services.
9. Verify external output by reading it back from the destination; a successful
   write response alone may be insufficient.
10. Record factual evidence without credentials, account addresses, connection
    IDs, or unnecessary object identifiers.
11. Preserve Git commit ordering and push the completed history to the required
    submission remote.

### Verified engineering findings

- Workspace skills were discovered and reported ready without reinstalling
  OpenClaw.
- The working integration is the native OpenClaw-managed Zapier MCP server, not
  the stale mcporter registry.
- Google Docs creation succeeded through Zapier's write executor and was
  independently found by exact-title read-back.
- Google Calendar accepted timezone-less input but stored the first event one
  hour late. The write response therefore did not prove the intended outcome.
- Complete RFC 3339 values with the applicable Toronto UTC offset prevented
  ambiguous Calendar time handling.
- The incorrect event was updated in place and read back; no duplicate was
  created.
- `openclaw doctor`, skill inspection, the Zapier probe, and Telegram channel
  status provide different evidence and should be used together.

### Failure-handling lessons

- Diagnose an ambiguous external result before retrying; blind retries can
  create duplicate documents, events, emails, or messages.
- Compare returned values with requested values, especially dates, time zones,
  recipients, titles, and destinations.
- Treat transient MCP timeouts as unconfirmed health, then use a targeted probe
  and one proportionate retry to distinguish network delay from persistent
  failure.
- Preserve historical test evidence when the current persona or configuration
  later changes; annotate the transition instead of rewriting history.
- Keep execution separate from teaching. Complete requested work first, then
  teach or provide detail when Teresa asks.

### Reuse guidance

Reuse this pattern for projects that personalize an existing agent, add
workspace skills, exercise connected services, or require design-before-build
evidence. Adapt file paths, tool schemas, project requirements, destinations,
and verification methods to the current environment.

Do not reuse assumptions about:

- available services or authorization;
- Zapier action names or schemas;
- calendar time zones;
- the model's projected tool catalogue;
- project rubrics or required Git ordering; or
- the submission remote.

Inspect and verify those facts independently for every new project.

### Maintenance

- Keep secrets and private identifiers out of this log.
- Add only substantial, completed, and verified project findings.
- Update prior entries with dated corrections rather than silently replacing
  historical facts.
- Remove repository-specific deploy access when it is no longer needed.

## 2026-09-11 — Add native Telegram voice replies

### Problem and implemented solution

Teresa wanted Charles to retain normal Telegram text replies and also provide a
spoken version. The live OpenClaw installation already included Auto-TTS, so the
solution uses `messages.tts` with `auto: always`, `mode: final`, and the bundled
Microsoft provider. No additional service, dependency, account, API key, or
OAuth connection was introduced.

The configured Canadian English voice produces MP3 audio. In OpenClaw's normal
Telegram ingress-and-reply path, the final text remains visible and the
generated audio is added as a TTS supplement.

### Verification and operational findings

- OpenClaw accepted and validated the TTS configuration without a gateway
  restart.
- The gateway reported Auto-TTS enabled with Microsoft configured.
- A direct synthesis test produced a non-empty Telegram-compatible MP3.
- The existing bot successfully delivered a normal text test and a five-second
  spoken voice test to Teresa's Telegram chat.
- Telegram remained enabled, connected, and healthy in polling mode.
- The existing Zapier MCP probe remained healthy after the change.
- Replies shorter than 10 characters and replies containing structured media
  are intentionally skipped by Auto-TTS.
- The current long-lived Telegram conversation separately triggered the model
  provider's prompt-injection filter before reply generation. Start a fresh
  Telegram session with `/new` before final live paired-response verification;
  this is a conversation/provider issue, not a synthesis or Telegram delivery
  failure.

### Reuse guidance

For supported OpenClaw channels, inspect native `messages.tts` and channel voice
capabilities before adding an external speech service. Verify configuration,
synthesis, media integrity, channel delivery, and the complete inbound reply
path separately. Preserve text delivery when synthesis fails, and do not treat
component tests as proof of a complete automatic reply when an upstream model
or session failure prevents that path from running.
