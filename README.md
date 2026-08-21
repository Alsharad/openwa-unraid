# OpenWA — Unraid Docker Template

Unraid template for [OpenWA](https://github.com/rmyndharis/OpenWA), an open-source WhatsApp API
gateway. One container, SQLite, local storage, all state in one appdata folder.

Unofficial. Template only — the image is upstream's, unmodified.

## Install

```bash
curl -fsSL https://raw.githubusercontent.com/Alsharad/openwa-unraid/main/openwa.xml \
  -o /boot/config/plugins/dockerMan/templates-user/my-OpenWA.xml
```

**Docker → Add Container → Template: OpenWA → Apply**, then open `http://<tower-ip>:2785`.

Sign in with the key from `/mnt/user/appdata/openwa/.api-key`, or set **Master API key** (32+ chars)
before applying.

## What this template decides

- **Appdata** — `/mnt/user/appdata/openwa` holds sessions, databases and keys. Back it up.
- **Memory** — `--memory=2g` in _Extra Parameters_ fits 3–4 `whatsapp-web.js` sessions. Raise it for
  more, or switch to the far lighter `baileys` engine.
- **`CSP_UPGRADE_INSECURE_REQUESTS=false`** — required for plain HTTP, or the dashboard renders
  blank. Set it `true` behind a TLS reverse proxy, and fill in `TRUSTED_PROXIES`.
- **No PUID/PGID fields** — OpenWA ignores them and drops to a fixed internal user, so appdata ends
  up owned by uid 997. Don't run _Docker Safe New Permissions_ on it.
- **No TZ field** — Unraid sets the timezone itself; adding one overrides it with a blank and forces
  UTC.
- **No Docker socket** — `Docker not available` in the log is expected. Mapping it would make the
  container host-root-equivalent, and only the dashboard's built-in datastore toggles need it.
- **`SSRF_ALLOWED_HOSTS` is exposed but empty** — under _Advanced_. OpenWA blocks plugins from calling
  private addresses, so a plugin pointed at another container on your LAN (Jellyseerr, Sonarr, Home
  Assistant) fails with `Blocked internal address` until you list that host. Comma-separated, written
  exactly as it appears in the plugin's own URL setting — `192.168.1.50`, not `http://192.168.1.50:5055`.
  Prefer it over `WEBHOOK_SSRF_PROTECT=false`, which switches the protection off everywhere.

## Tested on

Unraid 7.3.2, Docker 29.5.3 — healthy, dashboard serving, read-only rootfs and dropped capabilities
holding.

## License

MIT — see [LICENSE](LICENSE).
