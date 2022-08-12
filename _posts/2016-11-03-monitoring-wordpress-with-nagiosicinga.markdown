---
layout: post
title: Monitoring Wordpress with Nagios/Icinga
date: '2016-11-03 19:38:39'
tags:
- tech
---

I have many WordPress installs on servers I maintain and the hardest part is making sure all the plugins, themes, and WordPress itself remain up to date.

I monitor my infrastructure with [Icinga2](https://icinga.org) so I thought I’d try creating a plugin that can let me know when there are updates for WordPress core, themes, or plugins. It is written in Ruby and requires optparse, json, and active\_support. The check uses [WP CLI](http://wp-cli.org) to query your WordPress install so that is required as well.

The check itself is pretty simple, though for my environment I have my different sites running as different users (instead of just the web server user) to sandbox them a bit. WP CLI will want the check run as the web server user so you need to pass in that user with the _–wwwuser_ option. You also need to supply the document root, _–docroot_. Finally, in order to run the check as different users the script needs to run with sudo. You can do that by adding something like this to your sudoers file:

`nagios ALL=(ALL) NOPASSWD: /usr/local/bin/check_wp.rb`

This gives the nagios user permission to run get plugin without requiring a password.

An example of running the check from the command line would be:

`sudo /usr/local/bin/check_wp.rb -u wpuser -d /var/www`

Add _–plugins_ or _–themes_ to check for updates to them.

The code for the plugin is below, but it is part of the repository here: [icinga\_plugins](https://github.com/sterlinganderson/icing_plugins)

<!--kg-card-end: markdown--><!--kg-card-begin: html--><script src="https://gist.github.com/strlng/999b00dbebf7ba721f568a74697dd93a.js"></script><!--kg-card-end: html-->