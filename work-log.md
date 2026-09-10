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

Status: **stopped — waiting for VPS password.** No SSH attempt yet. No files, services, or configs on the VPS have been read or changed.

Intended first inspect commands (after password is received; read-only):

```bash
ssh root@165.227.203.137
uname -a
hostname
systemctl status openclaw-gateway.service --no-pager
ss -tlnp | grep -E '18789|LISTEN'
ps aux | grep -E 'openclaw|mcporter|node' | grep -v grep
ls -la ~/.openclaw/
ls -la /root/.openclaw/workspace/config/ 2>/dev/null
cat /root/.openclaw/openclaw.json
cat /root/.openclaw/openclaw.json.backup 2>/dev/null
cat /root/.openclaw/workspace/config/mcporter.json 2>/dev/null
journalctl -u openclaw-gateway.service -n 200 --no-pager
openclaw --help 2>/dev/null || true
```

After those, determine real status of Tasks 1–6 from files, processes, and logs. Diagnose Task 6 from the system. Report before changing anything.
