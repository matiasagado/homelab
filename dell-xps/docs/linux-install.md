# Ubuntu LTS Installation on Dell XPS

**Date:** 2026-03-25  
**Machine:** Dell XPS 15 9510
**OS Version:** Ubuntu 24.04.4 LTS

## Linux Installation

1. Downloaded Ubuntu LTS ISO.
2. Created bootable USB with belenaEtcher.
3. Installed Ubuntu:
   - Partition layout: default partitioning
   - File system: ext4

## Initial Configuration

1. Installed updates

```bash
sudo apt update && sudo apt upgrade
```

2. Installed essential tools:

```bash
sudo apt install git curl wget build-essential htop tree net-tools unzip -y
```

3. Enabled Firewall

```bash
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status
```

4. Installed, enabled, and verified SSH server:

```bash
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh
ssh matiasagado@127.0.0.1
```

## SSH Prompt Customization

1. Show `(ssh)` in terminal only during SSH sessions

```bash
# ~/.bashrc
if [ -n "$SSH_CONNECTION" ] && [ -n "$SSH_TTY" ]; then
    PS1="(ssh) $PS1"
fi
```

### Problems Encountered

- Initial PS1 overrides color prompt
- Some leftover session variable caused incorrect prompt

## Problems & Fixes

| Problem                                  | What Went Wrong                                | How I Fixed It                          |
| ---------------------------------------- | ---------------------------------------------- | --------------------------------------- |
| SSH prompt not showing `(ssh)` correctly | PS1 override removed colors; leftover env vars | Added SSH-aware PS1 with `$PS1` prepend |

## Notes
