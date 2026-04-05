# Container-first sandbox runtime

This setup runs OpenCode in a containerized environment where:

- the container filesystem is ephemeral (`read_only: true` + `tmpfs`)
- persistent data is kept in named Docker volumes
- project access is explicitly scoped to one mounted workspace
- server auth is required via `OPENCODE_SERVER_PASSWORD`

## Files

- `docker-compose.sandbox.yml`: hardened runtime profile
- `.env.sandbox.example`: environment template

## Quick start

```bash
cd packages/opencode
cp .env.sandbox.example .env.sandbox
# edit .env.sandbox

docker compose --env-file .env.sandbox -f docker-compose.sandbox.yml up -d
```

Then open `http://localhost:${OPENCODE_PORT:-4096}`.

## Persistence model

Container layers are ephemeral by design. Persisted data is split into volumes:

- `opencode_data` -> `~/.local/share/opencode` equivalent
- `opencode_config` -> `~/.config/opencode` equivalent

This keeps credentials/session data across restarts while retaining an immutable runtime root filesystem.

## Hardening notes

- `cap_drop: [ALL]`
- `no-new-privileges`
- loopback-only published port (`127.0.0.1`)
- CPU/memory/PID limits

If you need remote access, put a TLS reverse proxy in front and keep `OPENCODE_SERVER_PASSWORD` set.
