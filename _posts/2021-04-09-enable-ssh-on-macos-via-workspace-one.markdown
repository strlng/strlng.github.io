---
layout: post
title: Enable SSH on macOS via Workspace ONE
date: '2021-04-09 17:05:00'
tags:
- tech
- mdm
- macadmin
- ws1
- macos
- remote
- admin
---

SSH can be enabled through Workspace ONE using Provisioning. I've adapted the information on setting up SSH via [Munki](https://munki.org) found [here](https://scriptingosx.com/2016/01/control-apple-remote-desktop-access-with-munki/).

## Create a Custom Attribute Profile

The first step is to get the status of SSH on your clients. Create a **Custom Attribute** profile in Workspace ONE. This profile then needs to be assigned to devices on which you want to enable SSH.

<figure class="kg-card kg-image-card"><img src=" __GHOST_URL__ /content/images/2021/04/custom_attribute_1.png" class="kg-image" alt loading="lazy" width="2000" height="1336" srcset=" __GHOST_URL__ /content/images/size/w600/2021/04/custom_attribute_1.png 600w, __GHOST_URL__ /content/images/size/w1000/2021/04/custom_attribute_1.png 1000w, __GHOST_URL__ /content/images/size/w1600/2021/04/custom_attribute_1.png 1600w, __GHOST_URL__ /content/images/2021/04/custom_attribute_1.png 2258w" sizes="(min-width: 720px) 720px"></figure><figure class="kg-card kg-image-card"><img src=" __GHOST_URL__ /content/images/2021/04/custom_attribute_2-1.png" class="kg-image" alt loading="lazy" width="2000" height="1316" srcset=" __GHOST_URL__ /content/images/size/w600/2021/04/custom_attribute_2-1.png 600w, __GHOST_URL__ /content/images/size/w1000/2021/04/custom_attribute_2-1.png 1000w, __GHOST_URL__ /content/images/size/w1600/2021/04/custom_attribute_2-1.png 1600w, __GHOST_URL__ /content/images/2021/04/custom_attribute_2-1.png 2286w" sizes="(min-width: 720px) 720px"></figure>

The Bash script below will check SSH on assigned systems and report back one of five possible values:

1. SSH is off
2. access set to 'All Users'
3. no group 'com.apple.access\_ssh'
4. admin not in com.apple.access\_ssh
5. SSH enabled

    #!/bin/bash
    
    PATH=/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/munki export PATH
    
    # Edit this variable with your local admin user name!
    admin_user="admin"
    
    ssh_group="com.apple.access_ssh"
    
    # Is SSH enabled
    if [[$(systemsetup -getremotelogin) = 'Remote Login: Off']]; then
        echo 'SSH is off'
        exit 0
    fi
    
    # Does a group named "com.apple.access_ssh" exist?
    if [[$(dscl /Local/Default list /Groups | grep "${ssh_group}-disabled" | wc -l) -eq 1]]; then
        echo "access set to 'All Users'"
        exit 0
    elif [[$(dscl /Local/Default list /Groups | grep "$ssh_group" | wc -l) -eq 0]]; then
        echo "no group '$ssh_group'"
        exit 0
    fi
    
    # does the group contain the admin group?
    admin_uuid=$(dsmemberutil getuuid -U $admin_user)
    if [[$(dscl /Local/Default read Groups/com.apple.access_ssh GroupMembers | grep "$admin_uuid" | wc -l) -eq 0]]; then
        echo ${admin_user} not in $ssh_group
        exit 0
    fi
    
    echo "SSH enabled"
    exit 0

## Create a new Provisioning Component File/Action

Next, a new provisioning component needs to be created to perform the SSH enable. The component is a **Files/Actions** component.

<figure class="kg-card kg-image-card"><img src=" __GHOST_URL__ /content/images/2021/04/provisioning_action_1.png" class="kg-image" alt loading="lazy" width="2000" height="1336" srcset=" __GHOST_URL__ /content/images/size/w600/2021/04/provisioning_action_1.png 600w, __GHOST_URL__ /content/images/size/w1000/2021/04/provisioning_action_1.png 1000w, __GHOST_URL__ /content/images/size/w1600/2021/04/provisioning_action_1.png 1600w, __GHOST_URL__ /content/images/2021/04/provisioning_action_1.png 2246w" sizes="(min-width: 720px) 720px"></figure><figure class="kg-card kg-image-card"><img src=" __GHOST_URL__ /content/images/2021/04/provisioning_action_2.png" class="kg-image" alt loading="lazy" width="2000" height="1329" srcset=" __GHOST_URL__ /content/images/size/w600/2021/04/provisioning_action_2.png 600w, __GHOST_URL__ /content/images/size/w1000/2021/04/provisioning_action_2.png 1000w, __GHOST_URL__ /content/images/size/w1600/2021/04/provisioning_action_2.png 1600w, __GHOST_URL__ /content/images/2021/04/provisioning_action_2.png 2272w" sizes="(min-width: 720px) 720px"></figure><figure class="kg-card kg-image-card"><img src=" __GHOST_URL__ /content/images/2021/04/provisioning_action_3.png" class="kg-image" alt loading="lazy" width="1764" height="932" srcset=" __GHOST_URL__ /content/images/size/w600/2021/04/provisioning_action_3.png 600w, __GHOST_URL__ /content/images/size/w1000/2021/04/provisioning_action_3.png 1000w, __GHOST_URL__ /content/images/size/w1600/2021/04/provisioning_action_3.png 1600w, __GHOST_URL__ /content/images/2021/04/provisioning_action_3.png 1764w" sizes="(min-width: 720px) 720px"></figure>

I've split the command out in to three steps.

    if [[$(systemsetup -getremotelogin) = 'Remote Login: Off']]; then /usr/sbin/systemsetup -setremotelogin On; fi

    if [[$(dscl /Local/Default list /Groups | grep "com.apple.access_ssh-disabled" | wc -l) -eq 1]]; then /usr/bin/dscl localhost change /Local/Default/Groups/com.apple.access_ssh-disabled RecordName com.apple.access_ssh-disabled com.apple.access_ssh; elif [[$(dscl /Local/Default list /Groups | grep "com.apple.access_ssh" | wc -l) -eq 0]]; then dseditgroup -o create -n "/Local/Default" -r "Remote Login Group" -T group com.apple.access_ssh; fi

    if [[$(dscl /Local/Default read Groups/com.apple.access_ssh GroupMembers | grep "$(dsmemberutil getuuid -U $ADMIN)" | wc -l) -eq 0]]; then dseditgroup -o edit -n "/Local/Default" -a $ADMIN -t user com.apple.access_ssh; fi

The **$ADMIN** variable should be changed to your admin user in this last step.

## Create the Product

Finally, a Product must be created to tie all this together.

<figure class="kg-card kg-image-card"><img src=" __GHOST_URL__ /content/images/2021/04/product_1.png" class="kg-image" alt loading="lazy" width="1584" height="1510" srcset=" __GHOST_URL__ /content/images/size/w600/2021/04/product_1.png 600w, __GHOST_URL__ /content/images/size/w1000/2021/04/product_1.png 1000w, __GHOST_URL__ /content/images/2021/04/product_1.png 1584w" sizes="(min-width: 720px) 720px"></figure>

Assign the product to devices or groups for which you want SSH enabled. The custom attribute profile will need to be assigned to these same groups.

An assignment rule must be created that checks the value of your custom attribute. Your Custom Attribute profile will be available to select as the **Attribute/Application**. The **Rule Logic** should be `SSHstatus@AirWatchAgent <> 'SSH enabled'`.

<figure class="kg-card kg-image-card"><img src=" __GHOST_URL__ /content/images/2021/04/product_2.png" class="kg-image" alt loading="lazy" width="2000" height="1323" srcset=" __GHOST_URL__ /content/images/size/w600/2021/04/product_2.png 600w, __GHOST_URL__ /content/images/size/w1000/2021/04/product_2.png 1000w, __GHOST_URL__ /content/images/size/w1600/2021/04/product_2.png 1600w, __GHOST_URL__ /content/images/2021/04/product_2.png 2258w" sizes="(min-width: 720px) 720px"></figure>

Under Manifest click ADD. **Action(s) To Perform** should be File/Action - Install  
**Files/Actions** should be **macOS enable SSH** (assuming you named the Action that)

<figure class="kg-card kg-image-card"><img src=" __GHOST_URL__ /content/images/2021/04/provisioning_action_3-1.png" class="kg-image" alt loading="lazy" width="1764" height="932" srcset=" __GHOST_URL__ /content/images/size/w600/2021/04/provisioning_action_3-1.png 600w, __GHOST_URL__ /content/images/size/w1000/2021/04/provisioning_action_3-1.png 1000w, __GHOST_URL__ /content/images/size/w1600/2021/04/provisioning_action_3-1.png 1600w, __GHOST_URL__ /content/images/2021/04/provisioning_action_3-1.png 1764w" sizes="(min-width: 720px) 720px"></figure>

Be sure to activate the Product. Any devices assigned should have SSH enabled for the admin user.

