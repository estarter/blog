---
layout: post
title: shortcut to toggle fn keys in macOS
date: 2017-01-10 20:03:53
categories: mac
---

Problem: create a shortcut to toggle "Use F1, F2, etc. keys as standard function keys" in macOS Sierra.

Here's the solution.

1. Download [fntoggle](https://github.com/nelsonjchen/fntoggle/releases) program and save it to /usr/local/bin, make it executable.
2. In Automator create a new Service.
   1. Select "no input" for recieving
   2. Add "Run Shell Script" action with command
      `/usr/local/bin/fntoggle ; echo "ok"`
   3. Save it as "fntoggle"
3. In System Preferences/Keyboard/Shortcuts find fntoggle under Services, sub-section General. Click on right column (with text none) to assign new shortcut.
