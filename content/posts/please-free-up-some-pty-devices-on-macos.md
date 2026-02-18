---
title: "Fix 'Please free up some pty devices' on macOS"
date: 2026-02-18T10:40:35+02:00
dscription: "Fix 'Please free up some pty devices' on macOS"
tags: [Tutorial, AI, Mac, Terminal, Tmux, Claude code, Oh My Claude]
categories: ["Tutorials", "Drupal 8"]
draft: false

featuredImage: ""
featuredImagePreview: ""

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: falsez
lightgallery: true
ruby: true
fraction: true
fontawesome: true
linkToMarkdown: true
rssFullText: false
code:
  copy: true
  # ...
math:
  enable: true
  # ...
mapbox:
  accessToken: ""
  # ...
share:
  enable: true
  # ...
---

# Fix "Please free up some pty devices" on macOS

If you're running tools that spawn many subprocesses (AI agents, tmux sessions, SSH connections), you may hit the default pseudo-terminal (pty) limit on macOS.

## Check your current limit

```bash
sysctl kern.tty.ptmx_max
```

Default is **511**. Check how many are in use:

```bash
ls /dev/ttys* | wc -l
```

If the count is near or above the max, you're out of ptys.

## Increase the limit

The maximum accepted value depends on your macOS / Darwin version. Try in order:

```bash
sudo sysctl -w kern.tty.ptmx_max=999
```

If that returns "Invalid argument", fall back to **512**:

```bash
sudo sysctl -w kern.tty.ptmx_max=512
```

> **Note:** On Darwin 24.x (macOS 15 Sequoia), the kernel rejects values above a hard cap (e.g. 1023 fails). Use the highest value your system accepts.

This takes effect immediately. No reboot needed.

## Make it persistent

Create or append to `/etc/sysctl.conf` so the setting survives reboots (use whichever value worked above):

```bash
echo 'kern.tty.ptmx_max=999' | sudo tee -a /etc/sysctl.conf
```

## Clean up stale processes

Orphaned processes can hold ptys open. Find them:

```bash
ps aux | grep -E 'node|tmux|ssh|claude' | head -20
```

Kill anything you don't recognize or no longer need:

```bash
kill <PID>
```

## Verify

```bash
sysctl kern.tty.ptmx_max
# kern.tty.ptmx_max: 999
```

That's it. You're back in business.


R
