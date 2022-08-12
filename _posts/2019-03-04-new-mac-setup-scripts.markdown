---
layout: post
title: New Mac Setup Scripts
date: '2019-03-04 19:32:47'
tags:
- tech
- macadmin
- macos
---

I usually reformat my laptop a couple times a year. It's completely unnecessary but I just get the itch to clear out and start over. To make that a little less annoying I've cobbled together several scripts that do much of the work for me. My laptop is in Apple's Device Enrollment Program so it's added to my MDM when it first boots. After the initial user setup is done I plug in my flash drive, and double click the go.command file to kick things off. This is the base script that starts everything else. It does a few things:

1. Install xcode command line tools
2. Copy various config files. Some of these are in the `private` folder which is not pushed to GitHub.
3. Run the other scripts.01-homebrew-install.sh - Install [HomeBrew](https://brew.sh/) and a couple packages02-terminal.sh - Set up the Terminal how I like it. This one does a bunch. It installs the Source Code Pro font and the [Base16](https://github.com/chriskempson/base16) color scheme for vim and Terminal.03-macos-app-store.sh - Installs mas command line tool, prompts user to sign in to Mac App Store, then installs a bunch of app store applications.04-munki.sh - I use [Munki](https://www.munki.org/) to manage my fleet and this script adds several applications the the SelfServeManifest so munki will install them.05-games.sh - Finally, this install the Battle.net application and runs it.

The full Github repository of my scripts (minus the private folder) can be found here: [https://github.com/strlng/macos-setup](https://github.com/strlng/macos-setup)

