---
layout: post
title: Installing (or re-installing) NVidia drivers for the NVidia Quadro 600 graphic card on SLC6 (RHEL 6)
---

This is basically a note for myself, to install or re-install the Nvidia drivers for the Quadro 600 graphics card on my SLC6 (RHEL 6) machine.

----

NVidia Quadro 600 Graphics card: <http://www.nvidia.com/object/product-quadro-600-us.html>

Drivers: 
 * Go to <http://www.nvidia.com/download/driverResults.aspx/134262/en-us>
 * Click on the green "Download" button, this will get you to a confirmation page
 * On the confirmation page, right-click and copy the link localition from the green "Agree&Download" button

----

Open a terminal in your SLC6 machine, and paste the copied link to `wget` to download the driver installer; then, run the installer:

    wget http://www.nvidia.com/content/DriverDownload-March2009/confirmation.php?url=/XFree86/Linux-x86_64/390.59/NVIDIA-Linux-x86_64-390.59.run&lang=us&type=TITAN
    sh NVIDIA-Linux-x86_64-390.59.run
    
The installer will download and install the drivers, then it will ask you if you want to install 32 bits libraries as well, and then it will ask you if you want the X configuration updated. 
Usually, I answer YES to all these questions.

Now, you can reboot the machine:

    reboot NOW
    
    
DONE! Drivers installed, enjoy your graphics card! :)


