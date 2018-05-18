---
layout: post
title: 'Installing Ubuntu in dual-boot mode along with RedHat Enterprise 6 / SLC6'
---

I have a machine running SLC6 (RHEL 6) and I needed to install Ubuntu to make some tests. To install it, [I made a bootable USB stick](http://www.riccardomariabianchi.com/creating-a-bootable-usb-drive-on-redhat-6.html). But then, I found that not all the Ubuntu versions installed correctly. Here below the outcome of the tests I did.

Just tested:

 * Ubuntu 16.0.4 installs fine and it correctly sees the SLC6 operating system already installed on the machine. Grub is configured correctly for a multi-boot.
 * Ubuntu 14.0 installs fine as well, as above.
 * Ubuntu 18.0.4 installation gives errors at the end and the multi-boot is not configured coorectly ==> machine is left in a broken state, with no grub correctly set and no way to easily  any OS, neither the new one, nor the previously existing one. only solution I found: reinstalling one of the two above versions of Ubuntu from a USB flash drive ==> That fixed the Grub multi-boot loader for all the operating systems on the machine.

**One important note!** 

Once successfully installed Ubuntu 16.0.4, I had problems in properly restarting the SLC6 operating system installed on the same machine. The OS correctly started, but then the boot stopped after the message

    certmonger     [OK]
    
I found that I could log into the machine from another machine, thus I realized there was a problem in starting the graphical driver/session.

To solve that, I logged into the machine as `root`, and [I re-installed the NVidia drivers](http://www.riccardomariabianchi.com/installing-nvidia-quadro-600-drivers-redhat-6-slc6.html) for the Nvidia Quadro 600 graphic card installed on that machine. Then, I restarted the machine. 

That solved the problem and I could properly start my SLC6 OS again.


----

What I did to create a bootable USB memory stick for Ubuntu on RedHat 6 (SLC6):

<http://www.riccardomariabianchi.com/creating-a-bootable-usb-drive-on-redhat-6.html>
 
----

General instructions to create a bootable USB flash drive:

 * On Windows: <https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-windows>
 * On Mac: <https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-macos>
 * On Linux (RedHat 6): <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/installation_guide/sect-making-usb-media>
