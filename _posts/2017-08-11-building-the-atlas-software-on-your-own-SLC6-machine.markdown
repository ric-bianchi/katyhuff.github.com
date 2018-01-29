---
layout: post
title: Building the ATLAS software on your own SLC6 machine
date: '2017-08-11 15:48:36'
tags:
- computing
- atlas
---

*This post is a WORK IN PROGRESS!*

### Table of Contents
{:.no_toc}

1. TOC
{:toc}

# Building Externals

## Setup

### Update the build scripts

The build of Externals in the nightly builds starts by cloning a specific version of the **AthenaExternals** project from GitLab.

Hence, if we need to modify the Externals, before running the script we should create a feature branch on GitLab, where we can modify the code.

Let's start making our own copy of the code, by forking the ATLAS Externals repository on GitLab: https://gitlab.cern.ch/atlas/atlasexternals.
If you don't know how to fork and clone the ATLAS software, please take a look at this piece of documentation: https://atlassoftwaredocs.web.cern.ch/gittutorial/.

Now that you have a clean clone of the ATLAS Externals on your machine, let's start working on that.

For example, if we only want to compile a subset of the Externals packages, we could edit the file `atlasexternals/Projects/AthenaExternals/package_filters.txt`, to only
compile the packages we need.

Thus, we have to tell **Athena** which version of **AtlasExternals** to compile. To do that, we have to modify two files.

Let's suppose we have a branch called `myBranch` in our fork of **AtlasExternals**, and we want to build that to build other Athena packages on top of those externals.

We start adding the URL of our own GitLab repository to the script which sets the download of the AtlasExternals from GitLab: `athena/Projects/Athena/build_externals.sh`.

We modify the original version:

```bash
#Check out AthenaExternals from the right branch/tag:
${scriptsdir}/checkout_atlasexternals.sh \
    -t ${AthenaExternalsVersion} \
    -s ${BUILDDIR}/src/AthenaExternals 2>&1 | tee ${BUILDDIR}/src/checkout.AthenaExternals.log
```

And this is the modified version, where we added the URL of our own GitLab  repository:

```bash
#Check out AthenaExternals from the right branch/tag:
${scriptsdir}/checkout_atlasexternals.sh \
    -t ${AthenaExternalsVersion} \
    -s ${BUILDDIR}/src/AthenaExternals 2>&1 \
    -e https://:@gitlab.cern.ch:8443/rbianchi/atlasexternals.git | tee ${BUILDDIR}/src/checkout.AthenaExternals.log
```

Then, we have to tell the Athena script which tag or branch we want to clone and compile; we do that by updating the file `athena/Projects/Athena/externals.txt`.

This is the original version:

```bash
AthenaExternalsVersion = 6cbde55c
GaudiVersion = v28r2.006
```

And here the modified version, where we set the `AthenaExternalsVersion` to the name of our custom branch:

```bash
AthenaExternalsVersion = origin/master-soqt-qt5LCG
GaudiVersion = v28r2.006
```

Now, when we will start the build of the Externals from Athena, our custom branch will be downloaded from our own GitLab repository and that will be compiled instead of the standard version used in the official ATLAS nightly build.


## Build

```bash
setupATLAS
lsetup python -f # needed if custom versions of Python are set in your system, like Anaconda
asetup none,gcc62 --cmakesetup
./athena/Projects/Athena/build_externals.sh -c # this will create a 'build' folder
```

After the build has finished, we can check that the build succeeded by checking the output returned by the `build_externals.sh` script; we can check that by typing this

```bash
echo $?
```

after the run of the script. The script should return `0`.

Moreover, we can check that Gaudi was build at:

```bash
build/install/Gaudi
```

In fact, Gaudi is not even tried to be built if the build of the other Externals does not succeed.

----

## Working on the AthenaExternals build

Sometimes something could go wrong in the CMake configuration, and you might want to try different settings in the CMakeLists.txt file of an External module.

Let's say, for example, that the configuration for the **EXPAT** external package does not work, and the package is not found during the CMake configuration; Thus, you want to try to fix that.

Expat is one of the external packages we directly pick from the LCG release, without compiling them by our own. So the configuration is stored in

Usually, the build script has to download the AthenaExternals configuration from GitLab, for a clean build. So, when you finished testing your new fix in your local build of AthenaExternals and you want to test your new settings with Athena, please remember to push your changes to the branch to which the `build_externals.sh` script is pointed, otherwise your new changes will not be taken into account.

----

## If you see those errors...

### template argument for non-type template parameter must be an expression

I got this error from the package If you see this error:

```bash
[313/41133] Generating GeoPrimitivesDictReflexDict.cxx
FAILED: DetectorDescription/GeoPrimitives/CMakeFiles/GeoPrimitivesDict.dsomap DetectorDescription/GeoPrimitives/CMakeFiles/GeoPrimitivesDictReflexDict.cxx x86_64-slc6-gcc62-opt/lib/libGeoPrimitivesDict_rdict.pcm
cd /home_slc6/rbianchi/code_local
[...]
sh /home_slc6/rbianchi/code_local/vp1_rel21_puttingEverythinTogether_CoinAtlas/build/build/Athena/DetectorDescription/GeoPrimitives/CMakeFiles/makeGeoPrimitivesDictReflexDict.sh
input_line_9:91:119: error: template argument for non-type template parameter must be an expression
  Eigen::internal::special_scalar_op_base<Eigen::Block<const Eigen::Matrix<double,4,4,0,4,4>,3,1,false>,double,double,Eigen::DenseBase<Eigen::Block<const Eigen::Matrix<double,4,4,0,4,4>,3,1,false> >,false> m_ss4;
                                                                                                                      ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/usr/include/eigen3/Eigen/src/Core/util/XprHelper.h:370:15: note: template parameter is declared here
         bool EnableIt = !is_same<Scalar,OtherScalar>::value >
```

Please notice that we got this error because our build machine has its own installation of Eigen under `/usr/include` and that version was taken by CMake during the configuration of the build.

Please remember: your build machine should be as clean as possible, otherwise the local custom installations of packages like Eigen, Qt, Coin, and so forth, are taken instead of those from LCG, which are the ones tested to correctly build the ATLAS software.

### "fatal error: expat.h: No such file or directory - #include <expat.h>"

```bash
[1037/26347] Building CXX object Tools/XMLCoreParser/CMakeFiles/XMLCoreParser.dir/src/XMLCoreParser.cxx.o
FAILED: Tools/XMLCoreParser/CMakeFiles/XMLCoreParser.dir/src/XMLCoreParser.cxx.o
[...]
fatal error: expat.h: No such file or directory
 #include <expat.h>
```

### "makeinfo is missing"

The program `makeinfo` is part of the package `tex-info`, ad it is needed to compile `Gdb`.

Apparently, `text-info` is not installed by default on plain SLC6 machines.

If you get this error, or a similar one, please Take me to [refer to the note about `Gdb`](#note:gdb).


----

## Notes

#### <a name="note:gdb"></a> Gdb

You can't disable the build of Gdb, while building the externals, because it is needed by the package "AthenaAuditors".

And ATLAS builds a custom version of Gdb, since using the version from LCG gives errors while building the "AthenaAuditors" package.

If you get errors about "makeinfo" while building Gdb, you probably miss the "texinfo" package.

On SLC6, you should first install the "public-yum-ol6" yum repository. Create a file called `/etc/yum.repos.d/public-yum-ol6.repo` and copy-paste the text below, to set the repository to get the package from:


```bash
[ol6_latest]
name=Oracle Linux $releasever Latest ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/latest/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1

[ol6_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

[ol6_ga_base]
name=Oracle Linux $releasever GA installation media copy ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/0/base/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

[ol6_u1_base]
name=Oracle Linux $releasever Update 1 installation media copy ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/1/base/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

[ol6_u2_base]
name=Oracle Linux $releasever Update 2 installation media copy ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/2/base/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

[ol6_u3_base]
name=Oracle Linux $releasever Update 3 installation media copy ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/3/base/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

[ol6_u4_base]
name=Oracle Linux $releasever Update 4 installation media copy ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/4/base/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

[ol6_u5_base]
name=Oracle Linux $releasever Update 5 installation media copy ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/5/base/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

[ol6_u6_base]
name=Oracle Linux $releasever Update 6 installation media copy ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/6/base/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

[ol6_UEKR3_latest]
name=Latest Unbreakable Enterprise Kernel for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/UEKR3/latest/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

[ol6_UEK_latest]
name=Latest Unbreakable Enterprise Kernel for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/UEK/latest/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1

[ol6_UEK_base]
name=Unbreakable Enterprise Kernel for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/UEK/base/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

[ol6_MySQL]
name=MySQL 5.5 for Oracle Linux 6 ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/MySQL/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

[ol6_gdm_multiseat]
name=Oracle Linux 6 GDM Multiseat ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/gdm_multiseat/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

[ol6_MySQL56]
name=MySQL 5.6 for Oracle Linux 6 ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/MySQL56/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

[ol6_spacewalk20_server]
name=Spacewalk Server 2.0 for Oracle Linux 6 ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/spacewalk20/server/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

[ol6_spacewalk20_client]
name=Spacewalk Client 2.0 for Oracle Linux 6 ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/spacewalk20/client/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

# the following repositories are only available for x86_64 architecture systems
[ol6_ofed_UEK]
name=OFED supporting tool packages for Unbreakable Enterprise Kernel on Oracle Linux 6 ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/ofed_UEK/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0
priority=20

[ol6_playground_latest]
name=Latest mainline stable kernel for Oracle Linux 6 ($basearch) - Unsupported
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/playground/latest/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0
```

Then, you can search and install the `texinfo` package with:

```bash
yum search texinfo
yum install texinfo.x86_64
```

Now you should see the new command `/usr/bin/makeinfo`, and you should be able to build Gdb on your build machine.



# Compiling Athena

## Environment setup

```bash
mkdir build/build/Athena
cd build/build/Athena
lsetup python -f # needed if custom versions of Python are set in your system, like Anaconda
# those are not needed anymore, they were needed when wanted to get another LCG release than the standard one
# --> export LCG_RELEASE=/cvmfs/sft.cern.ch/lcg/releases
# --> asetup none,gcc62 --cmakesetup --noLcgReleaseBase
asetup none,gcc62 --cmakesetup
```
Now we can set the environment up; this will use our local build of the Externals:

```bash
source ../../../athena/Projects/Athena/build_env.sh # (a)
```
Now we can check everything is fine by checking the `GAUDI_ROOT` variable, which, at that point, should be correctly set pointing to our local folder:

```bash
$ echo $GAUDI_ROOT
/path-to-test-folder/athena/Projects/Athena/../../../build/install/GAUDI/22.0.0/InstallArea/x86_64-slc6-gcc62-opt
```

## Build configuration

At this point we can choose what builder to sue to build the Athena packages: we have the coice between **GNU Make** and **Ninja**

### Building with GNU Make


```bash
# this is not needed if Externals build was successful! --> export
cmake ../../../athena/Projects/Athena/ 2>&1 | tee cmake_config.log
```

### Building with Ninja

After the source of the `build_env.sh` script (see point `(a)` in a code snippet above), we can set Ninja by typing:

```bash
export PATH=/afs/cern.ch/work/k/krasznaa/public/Ninja/1.7.2.gcc0ea.kitware.dyndep-1/x86_64-slc6:$PATH
```

After that, we can run CMake to generate Ninja configuration packages:

```bash
cmake -G Ninja -DCTEST_USE_LAUNCHERS=TRUE ../../../athena/Projects/Athena/ 2>&1 | tee cmake_config.log
```

Then, we can build the full ATLAS Athena by typing:

```bash
ninja
```

If we want to only build few specific packages, we can use:

```bash
ninja Package_VP1Algs
```

where `VP1Algs` is teh name of one of the Athena packages.



## Notes

### Gaudi

If we want to use a nightly build of Gaudi instead of building our own version, we can find it at a path similar to that (the name of the nightly is based on the date of the build, so it must be changed):

```bash
export GAUDI_ROOT=/cvmfs/atlas-nightlies.cern.ch/repo/sw/master/2017-07-31T2251/GAUDI/22.0.0/InstallArea/x86_64-slc6-gcc62-opt # taking Gaudi from the nightly
```
