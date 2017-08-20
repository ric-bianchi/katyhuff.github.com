---
layout: post
title: How to setup an Athena nightly release
date: '2017-03-21 11:58:24'
---

Recently, with the migration of CERN software infrastructure from AFS to CVMFS, and from the build with the pair CMT+SVN to CMake+Git, the setup of an ATLAS *Athena* release has slightly changed.

To setup a nightly release to do code development, the instructions are now, for example, these:

    setupATLAS
    asetup 21.0.X-VAL,2017-03-20T2139

where:

* `setupATLAS` is the command to setup the ATLAS environment (you can find it on LXPLUS machines, and on all LXPLUS-like machines); 
* `asetup` is the command to set a release;
* `21.0.X-VAL,2017-03-20T2139` is the nightly release we want to use. Please notice that we don't use anymore the flags *rel_1*, *rel_2*, ... whaich were used before to designate the daily builds, because we now have a buffer of more many builds, so they are named with the daily timestamp. You can find all the available nightlies here: `ls /cvmfs/atlas-nightlies.cern.ch/repo/sw/21.0.X-VAL/`.

----

**More info:**

For more updated info, you can take a look at [this twiki page](https://twiki.cern.ch/twiki/bin/view/AtlasComputing/WorkBookSetAccount).