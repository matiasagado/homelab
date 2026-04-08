# SSH Key Authentication

ED25519 key-based authentication for passwordless SSH access to the XPS over Tailscale. Faster, more secure, and eliminates password prompts during remote sessions.

## Generate Key Pair (on Mac)

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

## Copy Public Key to XPS

```bash
ssh-copy-id user@<tailscale-ip>
```

## SSH Config (on Mac)

```
# ~/.ssh/config
Host dell-xps
    HostName <tailscale-ip>
    User matiasagado
```

With this config, connecting is a single command from any network:

```bash
ssh dell-xps
```

## XPS SSH Hardening

Key settings applied in `/etc/ssh/sshd_config` on the XPS:

```
PasswordAuthentication no
PermitRootLogin no
MaxAuthTries 3
```

Restart SSH after changes: `sudo systemctl restart ssh`
