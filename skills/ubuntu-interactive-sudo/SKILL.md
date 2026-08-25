---
name: ubuntu-interactive-sudo
description: Use Ubuntu's graphical polkit authentication dialog when available, with a terminal-attached sudo fallback, for package and system-install tasks requiring administrator authentication.
---

# Ubuntu Interactive Sudo

When a requested Ubuntu task needs administrator privileges, prefer a graphical polkit authentication dialog when running inside a desktop session (`DISPLAY` or `WAYLAND_DISPLAY` is set and `pkexec` is available). Run the narrowly scoped command through `pkexec`, for example:

```bash
pkexec /usr/bin/apt install -y /absolute/path/to/package.deb
```

The graphical prompt is provided by the desktop's polkit authentication agent; do not try to build a custom password dialog. If no desktop session or polkit agent is available, use `sudo` with an allocated terminal (`tty: true`) so it can authenticate interactively. Prefer the user's package manager and a narrowly scoped command.

Do not use `sudo -S`, capture a password, or ask the user to paste a password into chat or tool input. If a prompt cannot be serviced by the available UI or terminal interface, stop the waiting command and give the user the exact command to run locally, then verify the result afterward.

Before retrying after an interruption, check whether the download or package operation completed. After installation, verify the package status and executable with read-only checks such as `dpkg-query` and `command -v`. Keep downloaded installers in the task workspace unless the user specifies another location.

This skill applies to Ubuntu/Debian-style `apt` workflows; use the corresponding interactive terminal behavior with other Ubuntu administration commands as needed. It does not grant permission for a system change—the user must have requested the change and normal authorization still applies.
