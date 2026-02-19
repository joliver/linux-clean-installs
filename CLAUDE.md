# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Single bash script (`minimize-install`) that strips a Debian or Ubuntu cloud image down to a provider-agnostic baseline. It removes cloud-provider-specific packages, repos, configs, and services for 25+ providers (AWS, Azure, GCP, etc.), then rebuilds clean apt sources.

**Must run as root** on a Debian-based system.

## How It Works

1. Defines a whitelist of allowed packages (`_allowed_packages`)
2. Marks all installed packages as auto-removable, then marks whitelisted ones as manual
3. Runs `apt-get autoremove --purge` to remove everything not whitelisted
4. Deletes provider-specific files, caches, logs, identity files, and all of `/root`
5. Sets timezone to UTC and locks the root account
6. Writes `/etc/hosts` and regenerates machine-id
7. Writes clean apt sources in deb822 format (auto-detects Debian vs Ubuntu)
8. Runs `apt-get update`, `dist-upgrade`, and a final `autoremove --purge`

## Testing

No automated tests. To validate changes, run the script on a disposable VM or container built from a cloud image.

## Shell Conventions

- Uses `set -euo pipefail` (strict mode)
- Inline comments for `rm -rf` use backtick-quoted comment syntax (`` `# label` ``) for readability
