# Node.js Installation

> **Phase:** 0 — Foundation
> **Depends on:** [Linux Installation](linux-install.md)
> **Note:** Installed for scripting and dev use. Not required by any Docker service, services bring their own runtimes.

**Date:** 2026-03-26

## Version
- Node.js v20.20.0
- npm 10.8.2

## Installation
```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install nodejs -y
```

## Verification
```bash
node --version   # v20.20.0
npm --version    # 10.8.2
```
