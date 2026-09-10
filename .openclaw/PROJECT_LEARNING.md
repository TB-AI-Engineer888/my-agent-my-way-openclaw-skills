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
