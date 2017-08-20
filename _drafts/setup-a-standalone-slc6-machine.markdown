---
layout: post
title: Setup a standalone SLC6 machine
tags:
- computing
- cvmfs
- atlas
- sysadmin
---

Here below the instructions to install and setup CVMFS on a standalone machine. This is useful if you want to run, for example, the ATLAS software on your desktop machine or on your laptop.

### CVMFS

CVMFS is a software distribution service system used to distribute code, hosted and maintained centrally, to Grid Tier machines and to developers' machines.

All LHC experiments, plus the Geant collaboration, currently have their core software hosted on CVMFS.

![](http://stm0-softmugqic.hpc.mcgill.ca/service/doc/images/image01.png)

### Install CVMFS

To easily install and use CVMFS on your own desktop/laptop, you have to run a 64-bit machine with a kernel version > 2.6.32-358.11.1.el6.x86_64.

Info below are basically taken [from this page](https://twiki.cern.ch/twiki/bin/view/AtlasComputing/Cvmfs21#Install_cvmfs).

Instructions:

* create a partition for CVMFS cache; for example by using **LVM**:
    * `system-config-lvm` (install it with `yum` if you don't have it). [Info here](http://www.howtogeek.com/127246/linux-sysadmin-how-to-manage-lvms-with-a-gui/).
    * *add screenshot*
* Fetch and install the repo information (only needed first time):
    * `yum install https://ecsft.cern.ch/dist/cvmfs/cvmfs-release/cvmfs-release-latest.noarch.rpm`
* Install cvmfs:
    * `yum install cvmfs cvmfs-config-default cvmfs-auto-setup`
    * the command above install, setup and start CVMFS. After running YUM, you can test your running CVMFS typing:
        * `cvmfs_config chksetup` to check the configuration (it should print "OK")
        * `cvmfs_config showconfig` to show the configurations
        * `cvmfs_config probe` to check that the cvmfs mount point is working
        * `cvmfs_config stat` (can be run as normal user) to show cvmfs usage information (very useful!)
    * The info above is taken [from this page](https://twiki.cern.ch/twiki/bin/view/AtlasComputing/Cvmfs21) where you can find additional info as well (it might be accessible to ATLAS members only)  

* Create the file `/etc/cvmfs/default.local` and add these lines to it: 
    * `CVMFS_REPOSITORIES=atlas.cern.ch,atlas-condb.cern.ch,atlas-nightlies.cern.ch,sft.cern.ch`
    * `CVMFS_CACHE_BASE=/<path>/cache/cvmfs2  HELP Not needed if you use var/lib/cvmfs`
    * `CVMFS_QUOTA_LIMIT=45000 # should be equal 90% of the CVMFS partition size (in MB)`
    * `CVMFS_HTTP_PROXY="<CVMFS proxy server address>"`, with the quotation marks and the semicolons separating the fields if there is more than one server
    * **N.B.** to set the above variable, you can find an updated list of the proxy sites [at this page](https://twiki.cern.ch/twiki/bin/view/CvmFS/ClientSetupCERN)

Now, let us check the CVMFS configuration and its status:

* Check that *autofs* service is running, it is used to mount the CVMFS folder when accessed:
    * `service autofs start`
* Check the setup scripts: as root, typ `cvmfs_config chksetup`:
    * If you get an error like `Error: required parameter CVMFS_HTTP_PROXY undefined for atlas.cern.ch`, please check you have entered a correct server address in the CVMFS_HTTP_PROXY variable
    * If you get a warning like `Warning: failed to access http://cvmfs02.grid.sinica.edu.tw/cvmfs/atlas.cern.ch/.cvmfspublished through proxy http://ca-proxy1.cern.ch:3128`, it is probably harmless: it only warns you that some of the sites are not available, but CVMFS should contact the other available sites automatically anyway
* Check if the CVMFS sites and repositories are accessible: type `cvmfs_config probe` (even as normal user); you should get messages like `Probing /cvmfs/atlas.cern.ch... OK`
* Check the CVMFS status: type `cvmfs_config status` (as normal user); you should get messages like `atlas.cern.ch mounted on ...`
* If you go to the folder `/cvmfs`, you should now see a bunch of folders, one for each repository you have requested

At this stage, if you need, you can override some global configurations by following [these instructions](https://twiki.atlas-canada.ca/bin/view/AtlasCanada/ATLASLocalRootBase#Local_Overrides), which let you set specific Frontier-Squid servers. If you don't know what that is, probably you don't need to change the default settings.


### Setup the ATLAS environment

*  add this in your `~/.bash_profile`:
    * 
`export ATLAS_LOCAL_ROOT_BASE=/cvmfs/atlas.cern.ch/repo/ATLASLocalRootBase`
    * `alias setupATLAS='source ${ATLAS_LOCAL_ROOT_BASE}/user/atlasLocalSetup.sh'`
* And now simply type `setupATLAS` when you need to setup the ATLAS environment, as like as on the LXPLUS machines

---

*[^n] [Image source](http://stm0-softmugqic.hpc.mcgill.ca/service/doc/index.html)*