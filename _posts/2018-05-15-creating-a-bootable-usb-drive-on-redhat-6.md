
---
layout: post
title: 'How to create a bootable USB memory stick from an ISO file, on RedHat 6'
---

Today I needed to make a bootable USB from an ISO image file, on my Red Hat Enterprise 6 (SLC6) machine. In particular, I had to install Ubuntu on my RedHat machine. 

Here below the summary of all the steps:

1. Download the `.iso` file you need. (In my today's example, I downloaded the most recent Ubuntu's image file)
2. Attach a USB key. The USB key is usually auto-mounted and a Nautilus window will show you its content. The key will be erased, so remember to backup somewhere all the files stored in the USB stick. Notice the mount path; in my example it is:
    
        /media/KINGSTON
        
3. Check the name of the USB device:

        fdisk -l
        
   In my example, I get the device name `/dev/sdc`
4. Unmount the USB key with the terminal, **NOT** with Nautilus "Eject" (otherwise the `dd` command used on next steps will not see it properly):

        umount /media/KINGSTON

5. Copy the `.iso` file to the USB key (again: it will **ERASE** the USB key's content!), using the path of the iso file as input and the device name of the USB stick as output; the `bs` value is optional but setting it to a large value speeds up the process:

        dd if=/downloads/ubuntu-14.04-desktop-amd64.iso of=/dev/sdc bs=512k

   a) You can check the `bs` values of the input iso file with:

        isoinfo -d -i /downloads/ubuntu-14.04-desktop-amd64.iso | grep size
        ...
        Logical block size is: 2048

      Set the `bs` option to a larger value than that listed above.
6. Wait for `dd` to finish writing the image to the USB memory stick. The process is completed when you get an output like this below, and the command-line prompt appears again:

        1928+0 records in
        1928+0 records out
        1010827264 bytes (1.0 GB) copied, 102.146 s, 9.9 MB/s

7. Log out from the `root` account and unplug the USB drive (which should be still unmounted).
8. The USB memory stick is now ready to be used as a bootable device.

