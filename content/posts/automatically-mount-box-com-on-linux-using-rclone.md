---
title: "Automatically Mount Box.com on Linux Using rclone (Fedora 43)"
date: 2026-01-17
tags: ["linux", "rclone", "box", "fedora", "systemd", "box.com", "fedora43", "automation"]
categories: ["Infrastructure", "Linux", "Fedora"]
---


![Fedora, Box.com and rclone Banner](/images/auto-mount-box-on-linux.png)


## Overview

I need to rely on Box.com for daily work, but there is still no official desktop client for Linux, so I decided to try the much-praised **rclone** today.

This page documents a **complete, reproducible workflow** to:

- Connect Linux to Box.com using rclone  
- Mount Box as a local directory  
- Automatically mount it at boot using **systemd user services**

The reference system is **Fedora 43**.  
Notes are included where other Linux distributions may differ.

---

## Environment

- OS: **Fedora 43**
- Init system: **systemd**
- Mount method: **rclone mount (FUSE)**
- Scope: **user-level service**
- Box remote name (variable): `mybox`
- Local mount point: `~/mybox`

> All commands below consistently use the variable name `mybox`.

---

## 1. Install rclone

### Fedora
```bash
sudo dnf install -y rclone
```

**Verify installation:**
```bash
rclone version
```

## 2. Configure Box Remote

Start the rclone configuration wizard:
```bash
rclone config

```

Configuration summary:

1. Select n → New remote
2. Name: mybox
3. Storage type: box
4. Accept defaults unless you have special requirements
5. Complete OAuth authentication in the browser

**Verify the remote:**
```bash
rclone listremotes
```

**Expected output:**
```bash
mybox:
```

## 3. Test Box Connectivity
Before proceeding, **verify that the remote works correctly**:
```bash
rclone lsd mybox:
```
If folders are listed successfully, authentication and API access are confirmed.


## 4. Create Local Mount Directory
```bash
mkdir -p ~/mybox
```

## 5. Create systemd User Service for Auto-Mount
### 5.1 Create systemd user directory (if missing)
```bash
mkdir -p ~/.config/systemd/user
```

### 5.2 Create the service file
```bash
nano ~/.config/systemd/user/rclone-mybox.service
```

### 5.3 Service definition (Fedora 43 example)

```ini
[Unit]
Description=Rclone Mount Box (mybox)
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
ExecStart=/usr/bin/rclone mount mybox: %h/mybox \
    --vfs-cache-mode writes \
    --dir-cache-time 1h \
    --poll-interval 1m \
    --umask 002 \
    --log-level INFO
ExecStop=/usr/bin/fusermount3 -u %h/mybox
Restart=on-failure
RestartSec=10

[Install]
WantedBy=default.target
```

## 6. Linux Distribution Differences
`fusermount` vs `fusermount3`

- Fedora / newer distributions: fusermount3

- Older Debian / Ubuntu: fusermount

Check which binary exists:
```bash
command -v fusermount3 || command -v fusermount
```

Update ExecStop accordingly:
```ini
ExecStop=/usr/bin/fusermount -u %h/mybox
```

or
```ini
ExecStop=/usr/bin/fusermount3 -u %h/mybox
```

## 7. Enable and Start the Service

> ⚠️ Do NOT use `sudo` with systemctl --user

Reload user units:
```bash
systemctl --user daemon-reload
```

Enable and start immediately:
```bash
systemctl --user enable --now rclone-mybox.service
```

Check status:
```bash
systemctl --user status rclone-mybox.service
```

Expected state:
```bash
Active: active (running)
```

## 8. Verify the Mount
```bash
mount | grep mybox || true
ls ~/mybox | head
```
If files are visible, the mount is successful.

## 9. Enable Auto-Mount at Boot (Without Login) [Optional]
By default, user services start only after login.
To mount Box **immediately** at boot, enable lingering:
```bash
sudo loginctl enable-linger $USER
```
This command only needs to be run once.


## 10. Troubleshooting and Logs
View service logs:
```bash
journalctl --user -u rclone-mybox.service -e
```

Restart or stop the service:
```bash
systemctl --user restart rclone-mybox.service
systemctl --user stop rclone-mybox.service
```

## Conclusion
Using **rclone + systemd** user services, Box.com can be:
- Mounted reliably on Linux
- Exposed as a local filesystem
- Automatically available at boot
- Maintained without an official Box client

This setup is robust, portable across distributions with minor adjustments, and suitable for long-term daily use.

See you in the next post ✌️

-Chrispy
