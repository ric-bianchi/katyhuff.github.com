---
layout: post
title: Updating the C/C++ compiler GCC on Ubuntu
---

I got a strange error this morning, on my Ubuntu workstation, from one of the packages of the ATLAS *Athena* software framework:


It turned out (Thanks, Scott, for the suggestion!!) that the GCC compiler installed on Ubuntu 16.0.4 was too old for the package to use the `` module.

The default GCC compiler isntalled on Ubuntu 16.0.4, in fact, is the version 5.4.0:

```bash
$ gcc --version
gcc (Ubuntu 5.4.0-6ubuntu1~16.04.10) 5.4.0 20160609
Copyright (C) 2015 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

I found very complete and detailed instructions on how to install an updated version of GCC on Ubuntu, here:

<https://gist.github.com/application2000/73fd6f4bf1be6600a2cf9f56315a2d91>

Thanks, Alexandre ELISÉ / application2000, for these instructions!!
I repeat them here, as a personal reference in case the Gist will be moved or deleted.

```bash
sudo apt-get update && \
sudo apt-get install build-essential software-properties-common -y && \
sudo add-apt-repository ppa:ubuntu-toolchain-r/test -y && \
sudo apt-get update && \
sudo apt-get install gcc-snapshot -y && \
sudo apt-get update && \
sudo apt-get install gcc-6 g++-6 -y && \
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-6 60 --slave /usr/bin/g++ g++ /usr/bin/g++-6 && \
sudo apt-get install gcc-4.8 g++-4.8 -y && \
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 60 --slave /usr/bin/g++ g++ /usr/bin/g++-4.8;

# When completed, you must change to the gcc you want to work with by default. Type in your terminal:
sudo update-alternatives --config gcc

# To verify if it worked. Just type in your terminal
gcc -v
```

Now, I can see the new compiler at work, in my shell:

```bash
$ gcc --version
gcc (Ubuntu 6.4.0-17ubuntu1~16.04) 6.4.0 20180424
Copyright (C) 2017 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

$ g++ --version
g++ (Ubuntu 6.4.0-17ubuntu1~16.04) 6.4.0 20180424
...
```

And now, the compilation of the ATLAS package just works...! 

Thanks again, Scott and Alexandre!
