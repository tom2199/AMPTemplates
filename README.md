# Enshrouded (ARM64 / Hangover) — AMP Generic Module template

Runs the Enshrouded dedicated server on **ARM64/aarch64** Linux (e.g. Oracle Cloud Ampere) via **Hangover** (Wine compiled natively for ARM64, with FEX/Box64 for x86-64 Windows code).

There is no native Linux or ARM build of the Enshrouded server, so this template runs the Windows `enshrouded_server.exe` through Hangover's `wine` — no x86-64 Proton/box64-of-Proton needed.

## Prerequisite (one-time, as root)

Hangover must be available on the host at `/opt/hangover/usr/bin/wine`. Folder-only extraction (no `apt install`, no system Wine):

```bash
cd /tmp
curl -fLO https://github.com/AndreRH/hangover/releases/download/hangover-11.16/hangover_11.16_ubuntu2404_noble_arm64.tar
tar xf hangover_11.16_ubuntu2404_noble_arm64.tar
sudo mkdir -p /opt/hangover
for deb in hangover-*.deb; do sudo dpkg-deb -x "$deb" /opt/hangover; done
sudo chmod -R a+rX /opt/hangover
/opt/hangover/usr/bin/wine --version   # should print: wine-11.16 (Hangover)
```

If `ldd /opt/hangover/usr/bin/wine | grep "not found"` lists libraries, install them (e.g. `sudo apt install -y libfreetype6 libgnutls30 libglib2.0-0t64`).

## Notes

- **Host mode only.** The instance must run on the host (not Docker), because Hangover lives at `/opt/hangover`.
- **Port:** UDP 15637 (QueryPort). Open it in your cloud security list and firewall.
- **Config:** the stock `enshrouded_server.json` settings UI is reused unchanged.
- The template is adapted from the stock `enshrouded` template; the only material changes are the executable (Hangover `wine`), the environment variables (`WINEPREFIX`/`WINEDEBUG`), the monitor regex, and the removal of the Proton GE download stage.

## Install into AMP

1. In AMP: **Configuration → Instance Deployment → Add Configuration Repository**, enter `YOUR_GITHUB_USERNAME/YOUR_REPO:main`, click **Fetch**.
2. Create a new instance; it appears with the `hangover` prefix.
