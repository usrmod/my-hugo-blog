
---
draft: false
title: "Persistent Tmux"  # Hugo archetype templating
date: "2026-02-10T02:30:54+05:30"
author: "Nishant"
tags: ["tmux"]
categories: ["setup"]
showToc: true
TocOpen: false
hidemeta: false
comments: false
description: ""          # Short description shown under the title on the post page on expanded post
summary: "Make your Tmux sessions survive reboots"              # Summary shown when post is collapsed
canonicalURL: ""         # Example: "https://canonical.url/to/page"
disableShare: false
hideSummary: false      # true = hide the summary/excerpt on list pages
searchHidden: false     # true = hide from search index (if you enabled search)
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
UseHugoToc: true
cover:
  image: ""              # e.g. "cover.png" (with relative:true) or "/img/cover.png" or "https://..."
  alt: ""                # alt text for accessibility
  caption: ""            # caption under the cover image
  relative: true         # true for page bundles; false for absolute/URL paths
  hidden: false          # true = hide cover only on single post page
editPost:
  URL: ""                # e.g. "https://github.com/<user>/<repo>/blob/main/content"
  Text: "Suggest Changes"
  appendFilePath: true
---
<!--more-->
> TLDR;
> - Set up tmux-resurrect and tmux-continuum to save/restore sessions after reboots
> - Manual restore workflow: save before reboot, restore after with simple keybindings
---

# Making Tmux Survive Reboots

## Initial Setup

`tmux-resurrect` saves your complete tmux environment so you can restore it after a system restart. This includes sessions, windows, panes, layouts, and working directories.

The default keybindings are `prefix + Ctrl-s` to save and `prefix + Ctrl-r` to restore.

`tmux-continuum` adds background auto-saving every 15 minutes and *can* do auto-restore on tmux server start. We'll use it only for auto-save and keep manual restore control.

## Manual Installation (No TPM)

Both plugins support manual installation: clone the repos and load them via `run-shell` in your `~/.tmux.conf`.

Clone both plugins to a stable location:

```bash
git clone https://github.com/tmux-plugins/tmux-resurrect ~/opt/tmux-resurrect
git clone https://github.com/tmux-plugins/tmux-continuum ~/opt/tmux-continuum
```

Add these lines near the bottom of `~/.tmux.conf`:

```bash
# tmux-resurrect (manual install)
run-shell ~/opt/tmux-resurrect/resurrect.tmux

# tmux-continuum (manual install)
run-shell ~/opt/tmux-continuum/continuum.tmux
```

Reload your tmux config from inside a running session:

```bash
tmux source-file ~/.tmux.conf
```

## Enable Status Bar for Auto-Save

Continuum's periodic autosave requires tmux's status line to be enabled because it hooks into `status-right`.

If you've disabled the status bar, re-enable it:

```bash
set -g status on
```

Add this to `~/.tmux.conf` and reload.

## Keep Auto-Restore Disabled

To maintain manual control, simply **don't** add `set -g @continuum-restore 'on'` to your config. Continuum only auto-restores when this option is explicitly enabled.

Auto-restore (when enabled) happens exclusively on tmux server start, not when you re-source `~/.tmux.conf`.

## Your Reboot Workflow

Before rebooting (or anytime), manually save with:

```
prefix + Ctrl-s
```

You'll see "Tmux environment saved!" in the status bar.

After reboot, start tmux normally, then restore with:

```
prefix + Ctrl-r
```

Your sessions, windows, and panes will be recreated.

With continuum installed, background saves happen every 15 minutes automatically, so you won't lose more than 15 minutes of layout changes even if you forget to save manually.

## Next Steps

- If you control remote servers, install tmux there and standardize a "remote tmux everywhere" workflow
- Explore `tmux-resurrect` program restoration settings if you run specific tools in panes
- Consider adding a pre-shutdown hook to save tmux sessions automatically (requires systemd or equivalent)

## Verification

Test your setup:

```bash
# Create a test session with multiple panes
tmux new -s test
# Split some panes (Ctrl-b %)
# Save: prefix + Ctrl-s
# Kill tmux server
tmux kill-server
# Start fresh and restore
tmux
# prefix + Ctrl-r
```