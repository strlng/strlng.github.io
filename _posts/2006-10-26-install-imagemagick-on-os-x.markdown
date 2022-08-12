---
layout: post
title: Install ImageMagick on OS X
date: '2006-10-26 00:30:00'
---

It’s a good idea to keep all the source code you download in the same place. I place all mine in

/usr/local/src/

Open a terminal and create that directory:

$ sudo mkdir /usr/local/src $ sudo chown root:admin /usr/local/src $ sudo chmod g+wrx /usr/local/src

The second and third lines give your admin group permission to the folder. You’ll be able to stick your source in there without having to use sudo for absolutely everything. zlib The libraries and support apps need to be installed first. Let’s start with zlib as it’s needed by libpng. We can do it all from the terminal

$ curl -O http://www.zlib.net/zlib-1.2.3.tar.gz $ tar zxvf zlib-1.2.3.tar.gz $ cd zlib-1.2.3/ $ ./configure --prefix=/usr/local --shared $ make $ sudo make install $ cd .. libpng $ curl -O ftp://ftp.simplesystems.org/pub/libpng/png/src/libpng-1.2.12.tar.gz $ tar zxvf libpng-1.2.12.tar.gz $ cd libpng-1.2.12 $ ./configure --prefix=/usr/local $ make $ sudo make install $ cd .. libjpeg $ curl -O http://www.ijg.org/files/jpegsrc.v6b.tar.gz $ tar zxvf jpegsrc.v6b.tar.gz $ cd jpeg-6b/ $ cp /usr/share/libtool/config.sub . $ cp /usr/share/libtool/config.guess . $ ./configure --prefix=/usr/local --enable-shared --enable-static $ make $ sudo make install $ sudo ranlib /usr/local/lib/libjpeg.a $ cd ..

If you get an error like: install: /usr/local/man/man1/cjpeg.1: No such file or directory make: \* [install] Error 71 at the make install phase try the following command then run sudo make install again:

sudo mkdir /usr/local/man/man1 ImageMagick

Now for ImageMagick.

$ curl -O ftp://ftp.imagemagick.org/pub/ImageMagick/ImageMagick.tar.gz $ tar zxvf ImageMagick.tar.gz $ cd ImageMagick-6.3.0/ $ ./configure --prefix=/usr/local $ make $ sudo make install $ cd ..

You’ll want to make sure /usr/local/bin/ is in your path. Add the following to ~/.bash\_profile:

PATH="/usr/local/bin:$PATH" export PATH

Okay, open a new terminal window and you should have access to all the powerful ImageMagick tools. Check out the documentation here.

<!--kg-card-end: markdown-->