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

``` bash
#!/bin/bash

SOURCE="${BASH_SOURCE[0]}"
while [ -h "$SOURCE" ]; do # resolve $SOURCE until the file is no longer a symlink
  DIR="$( cd -P "$( dirname "$SOURCE" )" && pwd )"
  SOURCE="$(readlink "$SOURCE")"
  [[ $SOURCE != /* ]] && SOURCE="$DIR/$SOURCE" # if $SOURCE was a relative symlink, we need to resolve it relative to the path where the symlink file was located
done
DIR="$( cd -P "$( dirname "$SOURCE" )" && pwd )"

DIR="${DIR}/backup"

while :
do
	echo -e "\033[39mThis script will back up:\033[0m"
	echo ""
	echo -e "\033[33m****************************************************************\033[0m"
	echo -e "\033[34m	${HOME}\033[0m"
	echo -e "\tto"
	echo -e "\033[34m	$DIR"
	echo -e "\033[33m****************************************************************\033[0m"
	echo ""
	read -s -n1 -p "Is this correct? (press y/n)" key
	case $key in
		y)
		echo ""
		echo ""
		echo -e "\033[31m****************************************************************\033[0m"
		echo "You will receive several prompts asking to give Terminal"
		echo "access to files and data, you MUST approve these or"
		echo "the backup will not complete properly."
		echo -e "\033[31m****************************************************************\033[0m"
		sleep 2
		echo ""
		read -n1 -r -p "Press any key to begin..."
		echo ""
		echo ""
		echo "Starting backup, this may take a while..."
		echo ""
		mkdir -p "${DIR}"
		rsync --archive --verbose \
		--exclude ".Trash" \
		--exclude "Library/Application Support/SyncServices" \
		--exclude "Library/Caches" \
		--exclude "Library/Logs" \
		--exclude "Library/Preferences/com.apple.mail-shared.plist" \
		--exclude "Library/Preferences/com.apple.homed.plist" \
		--exclude "Library/Preferences/com.apple.homed.notbackedup.plist" \
		--exclude "Library/Cookies" \
		--exclude "Library/Metadata" \
		--exclude "Library/PersonalizationPortrait" \
		--exclude "Library/Containers" \
		--exclude "Library/Autosave Information" \
		--exclude "Library/IdentityServices" \
		--exclude "Library/Messages" \
		--exclude "Library/HomeKit" \
		--exclude "Library/Sharing" \
		--exclude "Library/Mail" \
		--exclude "Library/Accounts" \
		--exclude "Library/Safari" \
		--exclude "Library/Suggestions" \
		--exclude "Library/Application Support/CallHistoryTransactions" \
		--exclude "Library/Application Support/CloudDocs" \
		--exclude "Library/Application Support/com.apple.sharedfilelist" \
		--exclude "Library/Application Support/Knowledge" \
		--exclude "Library/Application Support/com.apple.TCC" \
		--exclude "Library/Application Support/FileProvider" \
		--exclude "Library/Application Support/com.apple.avfoundation" \
		--exclude "Library/Application Support/CallHistoryDB" "$HOME" "$DIR"
		echo ""
		echo "Done! You can run this again to backup any updated files. \
		Only updated files will be backed up so the backup should be considerably quicker."
		exit 0
		;;
		n)
		echo ""
		echo "Exiting..."
		exit 0
		;;
		*)
		echo ""
		echo "You need to pick Yes or No"
		continue
		;;
	esac
done
```