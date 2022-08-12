---
layout: post
title: Bash script for Rsync backups
date: '2021-04-15 18:45:00'
tags:
- tech
- macadmin
- macos
- admin
- script
- bash
---

I've got a very basic bash script I use for user backups to external drives. I place the script on an empty external drive and give that to my users. They are instructed to plug the drive in and double click the backup file. The script asks them to verify the &nbsp;folder being backed up (their home folder), and the destination (the external drive). There are no options, just yes or no to a single question. They then get a message telling them to give the terminal application permissions to folders when prompted, and the backup script just does it's thing. Since it's Rsync they can easily just re-run it if necessary and only changed files will get backed-up, saving time.

I also have [Code42 CrashPlan](https://code42.com) running on supported devices, so this script is used just as a safety when I deploy new devices to users. The users get theior device then can easily copy over needed files from the external drive to their new device. I like to do this instead of migrating accounts so the users end up with a more fresh system, and hopefully they leave some unneeded data behind on that external drive.

<!--kg-card-begin: html--><script src="https://gist.github.com/strlng/05779ce9a6096c96e2d235254b0025d6.js"></script><!--kg-card-end: html-->