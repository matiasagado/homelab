# Ubuntu LTS Installation

Ubuntu 24.04.4 LTS installed on the Dell XPS 15 9510 as the foundation for all homelab services. Covers OS install, initial system setup, UFW, and SSH server configuration.

## OS Install

1. Download the Ubuntu LTS ISO
2. Flash to USB with balenaEtcher
3. Boot from USB and install — default partitioning, ext4 filesystem

## Initial Setup

Update the system and install essential tools:

```bash
sudo apt update && sudo apt upgrade
sudo apt install git curl wget build-essential htop tree net-tools unzip -y
```

## Firewall

UFW is enabled with SSH allowed before starting the SSH server:

```bash
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status
```

## SSH Server

```bash
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh
```

## SSH Prompt Customization

Adding `(ssh)` to the terminal prompt only during SSH sessions makes it immediately clear when you're on the remote machine:

```bash
# ~/.bashrc
if [ -n "$SSH_CONNECTION" ] && [ -n "$SSH_TTY" ]; then
    PS1="(ssh) $PS1"
fi
```

## Known Issues and Tips

- **PS1 overrides can strip color.** Prepend `(ssh)` to `$PS1` rather than replacing it entirely to preserve the existing color prompt.
