# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Bash scripts that strip cloud images down to a provider-agnostic baseline. They remove cloud-provider-specific packages, repos, configs, and services for 25+ providers (AWS, Azure, GCP, etc.), then rebuild clean package sources.

- **`install-debian`** — targets Debian and Ubuntu (apt/dpkg)
- **`install-rpm`** — targets Fedora, CentOS Stream, RHEL, Rocky Linux, and AlmaLinux (dnf/rpm)
- **`install-arch`** — targets Arch Linux (pacman)

**Must run as root** on the respective distro.

## How It Works

1. Defines a whitelist of allowed packages (`_allowed_packages`)
2. Marks all installed packages as auto-removable, then marks whitelisted ones as manual
3. Removes everything not whitelisted (`apt-get autoremove --purge`, `dnf autoremove`, or `pacman -Rns`)
4. Deletes provider-specific files, caches, logs, identity files, and all of `/root`
5. Regenerates machine-id and SSH host keys
6. Sets timezone to UTC and locks the root account
7. Writes `/etc/hosts`
8. Writes clean package sources (deb822 for Debian/Ubuntu, yum repo files for RPM distros, pacman mirrorlist/config for Arch)
9. Runs dist-upgrade/distro-sync/`pacman -Syu` and a final autoremove

## Testing

No automated tests. To validate changes, run the script on a disposable VM or container built from a cloud image.

## Shell Conventions

- Uses `set -euo pipefail` (strict mode)
- Inline comments for `rm -rf` use backtick-quoted comment syntax (`` `# label` ``) for readability
