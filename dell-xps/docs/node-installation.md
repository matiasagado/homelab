# Node.js

Node.js v20 installed on the XPS via NodeSource for scripting and development use. Not required by any Docker service — containerized services bring their own runtimes.

## Installation

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install nodejs -y
```

## Versions

- Node.js: v20.20.0
- npm: 10.8.2

## Notes

- Installed system-wide, not via `nvm` — simpler for a single-user machine
- Available for automation scripts, local tooling, or testing service integrations
