---
layout: post
title: Creating DHCP Static Maps for OS X Server in the Terminal
date: '2015-10-31 22:29:02'
tags:
- tech
---

I was in the process of moving DNS and DHCP on my network at work to new Mac Mini servers. This is all much easier using a Linux server, but I had several Mac only applications I was planning on running on the servers as well, so I stuck with Macs. Apple has made working with DNS easy enough in 10.11 Server by allowing one to edit the host files for domains directly, and have everything still work if one wants to go through the Server app. DHCP isn’t so easy. I haven’t found a way to create static maps for DHCP clients by just editing a reservation file. It is possible to use the serveradmin command line utility however.

To create a DHCP static map a series of commands need to be entered through serveradmin. I could have made this even easier, but I’ve only got about 120 clients so I didn’t want to spend a ton of time getting something working. I keep all my client information in a spreadsheet so it was easy to pull a list of MAC addresses, IP addresses, and computer names. These three items need to be placed in to the script below. You can seen how they need to be formatted as a multidimensional array in the Python script on line 6.

<style>.gist table { margin-bottom: 0; }</style>

After copying in the information, and running the script you are left with output like this:

`dhcp:static_maps:_array_id:2d752303-4e67-438c-87c9-6594658bb761 = create dhcp:static_maps:_array_id:2d752303-4e67-438c-87c9-6594658bb761:en_address:_array_index:0 = "xx:xx:xx:xx:xx:xx" dhcp:static_maps:_array_id:2d752303-4e67-438c-87c9-6594658bb761:ip_address:_array_index:0 = "xxx.xxx.xxx.xxx" dhcp:static_maps:_array_id:2d752303-4e67-438c-87c9-6594658bb761:name = "hostname"`

There are four lines for each entry. To actually create the static maps run type the following in a terminal on your server:

`$ sudo serveradmin settings`

Paste your script output in to the terminal window then, and to leave the command block type control+D. If you are creating a large number of static maps the terminal may seem to pause for a moment. To check if your additions saved properly type:

`$ sudo serveradmin settings dhcp:static_maps`

Your new maps should also show up in the Server GUI application.