---
title: 'How to Disable the Auto-suspend in Debian GNOME'
date: 2024-12-04
categories:
  - blog
tags:
  - linux
  - debian
---
I recently ran into a super annoying issue with my Debian machine. I use this machine to run climate models that can take hours or even days to complete. But my system had other plans - it kept trying to go to sleep every a couple of minutes if it's idle!

I tried all the usual stuff from the main documentation - clicking through the GNOME settings, trying to find that elusive power management option, but nothing seemed to work. The machine just kept threatening to suspend itself with those persistent notifications.

After digging deeper and trying various solutions, I finally found the combination of steps that actually works. Here's how to permanently disable auto-suspend on Debian with GNOME.

## The Solution

It turns out we need to tackle this from two angles: systemd and GNOME power management. Here's what you need to do:

### 1. First, Tell systemd to Stop Handling Sleep States

First, we need to tell systemd to stop handling sleep-related tasks. Run these commands:

```bash
# Mask the sleep targets so systemd won't handle suspension
sudo systemctl mask sleep.target suspend.target

# Reload systemd to apply changes
sudo systemctl daemon-reload
```

### 2. Then, Disable GNOME Power Management Suspend Settings

Even with systemd configured, GNOME might still try to trigger suspend events. Let's shut that down:

```bash
# Disable automatic suspend when on AC power
gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type 'nothing'
gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-timeout 0

# Disable automatic suspend when on battery
gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-battery-type 'nothing'
gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-battery-timeout 0

# Change what happens when you press the power button
gsettings set org.gnome.settings-daemon.plugins.power power-button-action 'nothing'
```

## Why This Works

The trick here is that we're handling both layers of power management:
1. System level (systemd) - which handles the actual suspension
2. Desktop environment level (GNOME) - which handles the user interface and triggers

By disabling both, we ensure that neither systemd can suspend your system, nor can GNOME try to trigger a suspend event. This means your climate models (or whatever long-running tasks you have) can run without interruption! 🎉

## Verifying It Worked

You can verify your settings with these commands:

```bash
# Check if systemd sleep targets are masked
systemctl status sleep.target suspend.target

# Check your GNOME power settings
gsettings list-recursively | grep -i sleep
```

And that's it! No more annoying suspend notifications, and your machine will stay awake as long as you need it to. I hope this is helpful, and let me know if you have any questions.