# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

This repository contains Linux fixes for the Razer Blade 14 (2023) laptop — specifically, a systemd service that fixes internal speakers (Realtek ALC298 codec) by running `hda-verb` sequences that must be reapplied on every boot and after suspend/hibernation.

## Repository Structure

- [rb14_speakers/RB14_2023_enable_internal_speakers_ver2.sh](rb14_speakers/RB14_2023_enable_internal_speakers_ver2.sh) — The audio fix script. Auto-detects the ALC298 codec device under `/proc/asound/card*/codec#*`, then replays ~1999 `hda-verb` commands embedded in the file itself (read via `grep "^hda-verb" "$0"`).
- [rb14_speakers/install_rb14_speakers.sh](rb14_speakers/install_rb14_speakers.sh) — Installer/uninstaller. Must be run from the same directory as the fix script.
- [rb14_speakers/rb14_speakers_service.md](rb14_speakers/rb14_speakers_service.md) — Full Spanish-language documentation for service management.

## Key Commands

### Install / Uninstall

Both files must be in the same directory before running:

```bash
sudo bash rb14_speakers/install_rb14_speakers.sh install
sudo bash rb14_speakers/install_rb14_speakers.sh uninstall
```

The installer handles `alsa-tools` (`hda-verb`) installation automatically for apt, dnf, yum, pacman, zypper, and emerge.

### Service Management (after install)

```bash
systemctl status rb14-speakers.service
journalctl -u rb14-speakers.service -n 50 --no-pager
sudo systemctl restart rb14-speakers.service
```

### Troubleshooting: wrong audio device number

If `/dev/snd/hwC2D0` doesn't exist, find the correct device:

```bash
ls /dev/snd/hw*
sudo nano /usr/local/lib/rb14-speakers/fix.sh   # replace C2D0 with correct value
sudo systemctl restart rb14-speakers.service
```

## Architecture

The fix works in two parts:

1. **systemd service** (`/etc/systemd/system/rb14-speakers.service`) — runs after `sound.target` on boot with a 5-second delay (`ExecStartPre=/bin/sleep 5`) to ensure the HDA codec is fully initialized.
2. **systemd-sleep hook** (`/usr/lib/systemd/system-sleep/rb14-speakers` or `/lib/systemd/system-sleep/rb14-speakers`) — re-runs the fix script on `post/*` sleep events (suspend/hibernate recovery).

The fix script auto-detects the card number dynamically so it works regardless of which card slot the audio device is assigned.

## Distro Notes

The installer detects the correct sleep hook path:
- Fedora/newer distros: `/usr/lib/systemd/system-sleep/`
- Ubuntu/older distros: `/lib/systemd/system-sleep/`
