---
layout: post
title: Enabling Apple Remote Desktop via Workspace ONE
date: '2021-04-12 16:30:00'
tags:
- tech
- admin
- macadmin
- mdm
- macos
- remote
- ws1
---

This post is very similar to my last, [enabling SSH via Workspace ONE]( __GHOST_URL__ /enable-ssh-on-macos-via-workspace-one/). The process is identical, just the scripts have changed. Like the SHS post this is [adapted from information here](https://scriptingosx.com/2016/01/control-apple-remote-desktop-access-with-munki/).

## Create a **Custom Attribute** profile

The custom attribute profile will check the status of ARD on assigned devices, reporting back to WS1.

<figure class="kg-card kg-image-card"><img src=" __GHOST_URL__ /content/images/2021/04/custom_attribute_1-1.png" class="kg-image" alt loading="lazy" width="2000" height="1496" srcset=" __GHOST_URL__ /content/images/size/w600/2021/04/custom_attribute_1-1.png 600w, __GHOST_URL__ /content/images/size/w1000/2021/04/custom_attribute_1-1.png 1000w, __GHOST_URL__ /content/images/size/w1600/2021/04/custom_attribute_1-1.png 1600w, __GHOST_URL__ /content/images/2021/04/custom_attribute_1-1.png 2270w" sizes="(min-width: 720px) 720px"></figure><figure class="kg-card kg-image-card"><img src=" __GHOST_URL__ /content/images/2021/04/custom_attribute_2-2.png" class="kg-image" alt loading="lazy" width="2000" height="1464" srcset=" __GHOST_URL__ /content/images/size/w600/2021/04/custom_attribute_2-2.png 600w, __GHOST_URL__ /content/images/size/w1000/2021/04/custom_attribute_2-2.png 1000w, __GHOST_URL__ /content/images/size/w1600/2021/04/custom_attribute_2-2.png 1600w, __GHOST_URL__ /content/images/2021/04/custom_attribute_2-2.png 2300w" sizes="(min-width: 720px) 720px"></figure>

The bash script below will return a custom attribute with one of these values:

1. ARD not running
2. All Users Access Enabled
3. admin not ARD admin
4. ARD enabled

**Script/Command:**  
Make sure to the edit the admin\_user variable.

    #!/bin/sh
    
    #Edit this variable with your local admin user name!
    admin_user="admin"
    
    PATH=/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/munki export PATH
    
    # check wether ARD is running
    ardrunning=$(ps ax | grep -c -i "[Aa]rdagent")
    
    if [[$ardrunning -eq 0]]; then
    	echo "ARD not running"
    	exit 0
    fi
    
    # all Users access should be off
    all_users=$(defaults read /Library/Preferences/com.apple.RemoteManagement ARD_AllLocalUsers 2>/dev/null)
    
    if [[$all_users -eq 1]]; then
    	echo "All Users Access Enabled"
    	exit 0
    fi
    
    # check whether the admin account is privileged
    ard_admins=$(dscl . list /Users naprivs | cut -d ' ' -f 1)
    
    if [[$ard_admins != $admin_user]]; then
    	echo "$admin_user not ARD admin"
    	exit 0
    fi
    
    echo "ARD enabled"
    
    exit 0

## **Create a new Provisioning Component File/Action**

Next, crerate a new **Provisioning Component** of type **Files/Actions** to perform the ARD enable.

Add a new Files/Actions

<figure class="kg-card kg-image-card"><img src=" __GHOST_URL__ /content/images/2021/04/component_filesactions_1.png" class="kg-image" alt loading="lazy" width="2000" height="1486" srcset=" __GHOST_URL__ /content/images/size/w600/2021/04/component_filesactions_1.png 600w, __GHOST_URL__ /content/images/size/w1000/2021/04/component_filesactions_1.png 1000w, __GHOST_URL__ /content/images/size/w1600/2021/04/component_filesactions_1.png 1600w, __GHOST_URL__ /content/images/2021/04/component_filesactions_1.png 2250w" sizes="(min-width: 720px) 720px"></figure><figure class="kg-card kg-image-card"><img src=" __GHOST_URL__ /content/images/2021/04/component_filesactions_2.png" class="kg-image" alt loading="lazy" width="2000" height="1475" srcset=" __GHOST_URL__ /content/images/size/w600/2021/04/component_filesactions_2.png 600w, __GHOST_URL__ /content/images/size/w1000/2021/04/component_filesactions_2.png 1000w, __GHOST_URL__ /content/images/size/w1600/2021/04/component_filesactions_2.png 1600w, __GHOST_URL__ /content/images/2021/04/component_filesactions_2.png 2272w" sizes="(min-width: 720px) 720px"></figure>

I’ve split the process in to three **Run** Actions. You could probably do just one long one if you wanted.

<figure class="kg-card kg-image-card"><img src=" __GHOST_URL__ /content/images/2021/04/component_filesactions_3.png" class="kg-image" alt loading="lazy" width="1760" height="934" srcset=" __GHOST_URL__ /content/images/size/w600/2021/04/component_filesactions_3.png 600w, __GHOST_URL__ /content/images/size/w1000/2021/04/component_filesactions_3.png 1000w, __GHOST_URL__ /content/images/size/w1600/2021/04/component_filesactions_3.png 1600w, __GHOST_URL__ /content/images/2021/04/component_filesactions_3.png 1760w" sizes="(min-width: 720px) 720px"></figure>

The commands should be run in this order:

    /System/Library/CoreServices/RemoteManagement/ARDAgent.app/Contents/Resources/kickstart -configure -access -on -users $ADMIN -privs -all

$ADMIN should be replaced with your admin account short name

    /System/Library/CoreServices/RemoteManagement/ARDAgent.app/Contents/Resources/kickstart -configure -allowAccessFor -specifiedUsers

    /System/Library/CoreServices/RemoteManagement/ARDAgent.app/Contents/Resources/kickstart -activate

## Create a Product

A provisioning product must be created to tie this all together. You will want to assign this to a Smart Group that also is assigned the Custom Attribute profile you created at the top.

<figure class="kg-card kg-image-card"><img src=" __GHOST_URL__ /content/images/2021/04/product_1-1.png" class="kg-image" alt loading="lazy" width="1576" height="1678" srcset=" __GHOST_URL__ /content/images/size/w600/2021/04/product_1-1.png 600w, __GHOST_URL__ /content/images/size/w1000/2021/04/product_1-1.png 1000w, __GHOST_URL__ /content/images/2021/04/product_1-1.png 1576w" sizes="(min-width: 720px) 720px"></figure>
## 

#### Edit the Assignment Rules
<figure class="kg-card kg-image-card"><img src=" __GHOST_URL__ /content/images/2021/04/product_2-1.png" class="kg-image" alt loading="lazy" width="2000" height="1469" srcset=" __GHOST_URL__ /content/images/size/w600/2021/04/product_2-1.png 600w, __GHOST_URL__ /content/images/size/w1000/2021/04/product_2-1.png 1000w, __GHOST_URL__ /content/images/size/w1600/2021/04/product_2-1.png 1600w, __GHOST_URL__ /content/images/2021/04/product_2-1.png 2238w" sizes="(min-width: 720px) 720px"></figure>

Your Custom Attribute profile will be available to select as the **Attribute/Application**. The **Rule Logic** should be `ARDstatus@AirWatchAgent <> 'ARD enabled'`. The script in the profile will return different values to let you know why ARD isn’t set up right for the client. It won’t just return yes or no.

### Under Manifest click ADD

**Action(s) To Perform** should be File/Action - Install  
**Files/Actions** should be macOS enable ARD (assuming you named the Action that)

Be sure to activate the Product. Any devices assigned should have ARD enabled for the admin user.

