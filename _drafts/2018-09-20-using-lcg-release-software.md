
## Setup a whole, standalone LCG release

Using the native LCG tools, you can set a whole release by using:

source /cvmfs/sft.cern.ch/lcg/views

## Using a LCG release within the ATLAS environment

Within the ATLAS environment, the `asetup` command sets up a default LCG release for you; that release has been tested by the software experts a the ATLAS SW release coordinators, to check that the whole ATLAS code base is compatible with it.

So, for example, if you set up a "master" release suing the standard setup, today you end up with this:

---
layout: post
title: 'How to set and use a CERN LCG Software Release'
---

CERN maintains a software bundle called LCG, which contains many packages used in the scientific context.

...

There are other ways to set up the LCG software.

You can setup a specific package from a specific LCG release  built for a specific platform, by using the command `lsetup "lcgenv <args>"`, as shown here below (Thanks, Attila, for the example). In some cases, like with Qt5, this also sets the related environemt variables:

```bash
    [rbianchi@vp1-dev-slc6] $ lsetup "lcgenv -p LCG_94 x86_64-slc6-gcc62-opt Qt5"
    ************************************************************************
    Requested:  lcgenv ... 
     Setting up lcgenv HEAD ... 
      lcgenv -p LCG_94 x86_64-slc6-gcc62-opt Qt5 ...
    # Using globaly provided gcc from /cvmfs/sft.cern.ch/lcg/contrib
    # found package fontconfig-63041
    # found package expat-fe6d4
    # found package pkg_config-588e0
    # found package gperf-699d7
    # found package freetype-4f115
    # found package zlib-da225
    # found package glib-53b12
    # found package gettext-f094d
    # found package pcre-9b589
    # found package pkg_config-588e0
    # found package libffi-26487
    # found package libxkbcommon-94b1d
    # found package Python-2cba0
    # found package sqlite-9c81e
    >>>>>>>>>>>>>>>>>>>>>>>>> Information for user <<<<<<<<<<<<<<<<<<<<<<<<<
    ************************************************************************
    [rbianchi@vp1-dev-slc6]
    [rbianchi@vp1-dev-slc6] $ echo $QTDIR 
    /cvmfs/sft.cern.ch/lcg/releases/LCG_94/qt5/5.11.1/x86_64-slc6-gcc62-opt/
    [rbianchi@vp1-dev-slc6] $ echo $QTLIB 
    /cvmfs/sft.cern.ch/lcg/releases/LCG_94/qt5/5.11.1/x86_64-slc6-gcc62-opt/lib/
    [rbianchi@vp1-dev-slc6] $ echo $QT5_HOME 
    /cvmfs/sft.cern.ch/lcg/releases/LCG_94/qt5/5.11.1/x86_64-slc6-gcc62-opt
 ```
 
 ## How to build Athena with a different LCG release
 
 In order to build Athena, or an Athena package, witha different LCG release than the default one, you need to build AtlasExternals first, with a modified version of the CMakeLists.txt file, where the LCG release number is set (Thanks, Ed, for the pointer):
 
 <https://gitlab.cern.ch/atlas/atlasexternals/blob/master/Projects/AthenaExternals/CMakeLists.txt>
 
 After that, you can build Athena.
 
 It's quite a chore, however: the compilation takes a while, so be prepared.
 
 
 
