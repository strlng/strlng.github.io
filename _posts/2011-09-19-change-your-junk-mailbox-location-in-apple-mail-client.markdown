---
layout: post
title: Change your junk mailbox location in Apple Mail client
date: '2011-09-19 12:36:50'
---

Apple Mail uses the folder **Junk** by default for junk mail/spam. You get the nice little icon that looks like a paper bag with a recycle symbol on it. If your mail account diverts junk mail on the server side to a different folder you will end up to two different junk folders to check. The junk folder that your mail server diverts spam to, and the folder Apple Mail diverts spam to. For example, my work mail diverts junk mail to a folder called **Junk Mail**. I end up with a regular folder called **Junk Mail** with spam the server caught, and another folder called **Junk** which Apple Mail caught. You can consolidate these folders in to one by telling Apple Mail to use the same folder that your mail server does.

You can change your junk mail folder pretty easily by editing a plist file.

1. Quit Mail
2. Open the file **~/Library/Mail/V2/MailData/Accounts.plist** with Xcode, or another text editor.
3. Find the listing for the account you want to edit and change it’s **JunkMailboxName** to the name you want to use for junk mail. For my work mail account I changed it to **Junk Mail**.
4. Save the file and relaunch Mail.

The Junk folder for your account should now map to the Junk folder in Apple Mail.