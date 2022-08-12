---
layout: post
title: Setting up your Mac for PHP web development.
date: '2006-10-26 23:35:00'
---

I found myself again having to install necessary web development tools on my MacBook Pro so I thought this time I’d actually document the process. After following these instructions your machine should be pretty well equipped for PHP web development. I had instructions for Ruby on Rails in here, but I’ll post those as a separate entry later. Before you going any further you must have Xcode installed. You can get the most recent version from Apple here: [http://developer.apple.com/tools/xcode/](http://developer.apple.com/tools/xcode/) What we will be installing: MySQL Apache 2.2.3 Subversion PHP 5.1.6 Before we do anything there is a variable that must be set for later on when we set up Apache and Subversion. Run the following command assuming you are using the bash shell that the OS X Terminal app uses by default:

$ ac\_cv\_func\_poll=no; export ac\_cv\_func\_poll

This sets the environment variable ac\_cv\_func\_poll to no. If you are using a different shell the command would be different. It’s a good idea to keep all the source code you download in the same place. I place all mine in /usr/local/src/. Open a terminal and create that directory:

$ sudo mkdir /usr/local/src $ sudo chown root:admin /usr/local/src $ sudo chmod g+wrx /usr/local/src

The second and third lines give your admin group permission to the folder. You’ll be able to stick your source in there without having to use sudo for absolutely everything. MySQL This is nice and easy. Just go here and grab the latest version of MySQL 5.0. You most likely want to standard installer, unless you really know what you are doing. Install the database, the start up item, and the pref pane item. Apache 2.2.3

$ curl -O http://mirrors.ccs.neu.edu/Apache/dist/httpd/httpd-2.2.3.tar.gz $ tar zxvf httpd-2.2.3.tar.gz $ cd httpd-2.2.3/ $ ./configure --prefix=/usr/local/apache2 --enable-mods-shared=all --enable-ldap=shared --enable-auth-ldap=shared --enable-ssl=shared --enable-rewrite=shared --with-ldap --with-auth-ldap --with-ssl --enable-proxy=shared --enable-authnz-ldap=shared --with-authnz-ldap $ make $ sudo make install $ cd ..

I’ve created an OS X startup item that can be downloaded from this web site.

$ curl -O http://sterlinganderson.net/assets/2007/5/2/apache2startupitem.tar.gz $ tar zxvf apachestartupitemtar.gz $ cd apache2startupitem $ sudo ./install.sh $ cd ..

You also need to edit /etc/hostconfig. Add the following line: APACHE2WEBSERVER=YES Change to APACHE2WEBSERVER=NO to disable the startup item. Subversion Server Apache needs to be installed first. So if you skipped Apache go back and install it now.

$ curl -O http://subversion.tigris.org/downloads/subversion-1.4.0.tar.gz $ tar zxvf subversion-1.4.0.tar.gz $ curl -O http://www.webdav.org/neon/neon-0.25.5.tar.gz $ tar zxvf neon-0.25.5.tar.gz $ mv neon-0.25.5 subversion-1.4.3/neon $ cd subversion-1.4.0/ $ ./configure --prefix=/usr/local --with-apr=/usr/local/apache2/ --with-apr-util=/usr/local/apache2/ --with-apxs=/usr/local/apache2/bin/apxs --with-ssl $ make $ sudo make install $ cd ..

PHP 5.1.6 MySQL and Apache need to be installed first.

$ curl -O http://us2.php.net/distributions/php-5.1.6.tar.gz $ tar zxvf php-5.1.6.tar.gz $ cd php-5.1.6 $ ./configure --prefix=/usr/local/php5 --with-zlib --with-xml --with-gd --with-jpeg-dir=/usr/local --with-png-dir=/usr/local --with-mysqli=/usr/local/mysql/bin/mysql\_config --with-mysql=/usr/local/mysql --with-apxs2=/usr/local/apache2/bin/apxs --with-ldap --enable-thread-safety $ make $ sudo make install $ cd ..

Configuration We now need to edit the configuration files for Apache and PHP. Open /usr/local/apache2/conf/httpd.conf in an editor. Change the User and Group from daemon to www. This should be around lines 125 and 126. I also change the DocumentRoot(line 162) to my Sites directory. If you change the DocumentRoot make sure you change which is about 25 to your changed DocumentRoot. Change the code at line 223 from:

DirectoryIndex index.html

to

DirectoryIndex index.html DirectoryIndex index.html index.php

We need to tell Apache to respect PHP file types if PHP is enabled (around line 390):

# If php is turned on, we repsect .php and .phps files. AddType application/x-httpd-php .php AddType application/x-httpd-php-source .phps

For PHP do the following:

$ sudo cp /usr/local/src/php-5.1.6/php.ini-recommended /usr/local/php5/etc/php.ini

Edit /usr/local/php5/etc/php.ini, find the line that says display\_errors = Off and change it to display\_errors = On The easiest way to get everything started is just to reboot. Now you are set to do some PHP web development on your Mac. You’ve probably noticed I didn’t do anything with Subversion. I’ll post something on setting that up in the future.

<!--kg-card-end: markdown-->