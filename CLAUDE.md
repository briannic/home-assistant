# CLAUDE.md — agent guide for `home-assistant`

## What this repo is

Brian's Home Assistant configuration. **Sibling** of `infra`, `go-trade`, `go-wx`, `bnichols.xyz`. Owns *what HA does* — automations, scripts, scenes, custom components, themes. The HA container itself (compose file, nginx vhost, port reservations) lives in `infra` at `hosts/huron/third-party/home-assistant/`.

## Hard rules

1. **Never commit secrets.** Real values (Pushover keys, future API tokens, OAuth refresh tokens) live in 1Password. This repo only carries `secrets.yaml.example` as a template. The real `secrets.yaml` is git-ignored and lives only on huron at `/opt/home-assistant/config/secrets.yaml`.
2. **Never commit runtime state.** `.storage/`, `home-assistant_v2.db`, `home-assistant.log*`, `deps/`, `tts/`, `image/`, `backups/` are HA-managed files. The `.gitignore` enforces this — don't override.
3. **YAML over UI for source-of-truth.** Use the HA UI to draft automations, then copy the generated YAML into this repo. Don't let the UI-editor-only automations accumulate — they live in `.storage/` (git-ignored) and aren't reproducible.
4. **Touch nothing in `infra` from here.** Compose changes, nginx changes, port reservations belong in `infra`. Cross-repo edits need separate PRs in each.

## Where things live

| You're looking for | Look here |
|---|---|
| Top-level HA config | [`configuration.yaml`](configuration.yaml) |
| All automations | [`automations.yaml`](automations.yaml) |
| Reusable scripts | [`scripts.yaml`](scripts.yaml) |
| Scene definitions | [`scenes.yaml`](scenes.yaml) |
| Secret template (for fresh huron) | [`secrets.yaml.example`](secrets.yaml.example) |
| YAML-managed dashboards | [`dashboards/`](dashboards/) |
| Automation pitches / hardware backlog / project ideas | [`IDEAS.md`](IDEAS.md) |
| How HA gets *deployed* on huron | [`infra/hosts/huron/third-party/home-assistant/`](../infra/hosts/huron/third-party/home-assistant/) |
| Cross-app contract (port, URL, integration list) | [`infra/apps/home-assistant.md`](../infra/apps/home-assistant.md) |

## Key integrations

| Integration | Service / Entity pattern | Notes |
|---|---|---|
| TTS | `tts.cloud_say` | Nabu Casa Cloud TTS — `tts.google_translate_say` is **not** available |
| Sonos | `media_player.sonos_move` | Auto-discovered via SSDP (`default_config`); one speaker (Move) |
| Pushover | `notify.pushover` | Keys in `secrets.yaml` (1Password) |
| Z2M door sensor | `binary_sensor.0x00158d008c8b2049_door` | Aqara MCCGQ11LM; `on` = open, `off` = closed |

## Deploy loop

Config change → commit + push → `ssh huron 'cd /opt/home-assistant/config && git pull'` → reload from HA UI (Developer Tools → YAML → "Reload Automations" or "Restart").

**Gotcha:** The HA UI editor reformats `automations.yaml` on save (removes quotes, changes indentation, renames `trigger`→`triggers`). If someone edits via UI on huron, the local working tree will have uncommitted changes that conflict with `git pull`. Fix with `git stash && git pull` on huron — the repo version is the source of truth.

For container/image/network-level changes — that's `infra` territory.
