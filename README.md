# home-assistant

User-authored Home Assistant configuration for the HA instance running on huron.

## Where it runs

Container: `home-assistant` on huron (Docker). Web UI at `http://palmgren.local:3004/`.

The container bind-mounts `/opt/home-assistant/config/` (this repo's checkout on huron) to its internal `/config`. HA loads `configuration.yaml` at startup; most other files are pulled in via `!include`.

## Deployment-side stuff lives in infra

How the HA container is provisioned, the nginx vhost in front of it, the static-IP reservation conventions, and the cross-app index live in the [`infra` repo](../infra/) (sibling of this one) at `hosts/huron/third-party/home-assistant/` and `apps/home-assistant.md`. This repo only owns *what HA does*, not *how HA runs*.

## Layout

```
configuration.yaml      - top-level config; includes the rest
automations.yaml        - all automations
scripts.yaml            - reusable scripts
scenes.yaml             - scene definitions
themes/                 - UI themes (none yet)
custom_components/      - HACS or hand-installed integrations (none yet)
blueprints/             - automation blueprints (none yet)
secrets.yaml.example    - template for the git-ignored secrets.yaml
.gitignore              - HA runtime state (db, .storage, logs, secrets)
```

## Deploy a config change

```bash
# Edit, commit, push in this repo.
git commit -am "automation: morning music"
git push

# Pull on huron + reload HA.
ssh huron 'cd /opt/home-assistant/config && git pull'
ssh huron 'docker exec home-assistant python -c "import requests; requests.post(\"http://127.0.0.1:8123/api/services/automation/reload\")"'   # or simply Restart from the UI
```

For automation-only changes, "Reload Automations" in the HA UI (Developer Tools → YAML) suffices and avoids a full container restart.

## Secrets

`secrets.yaml` is git-ignored. Real values live in 1Password (`Personal` vault). When provisioning a fresh huron, copy `secrets.yaml.example` to `secrets.yaml` and fill in from 1Password. See `secrets.yaml.example` for the canonical key list.
