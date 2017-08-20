---
layout: post
title: Git/CMake development in ATLAS
---

With rel. 21, ATLAS officially migrated its software devops to CMake and Git

```bash
mkdir vp1; cd vp1
mkdir build source run
cd source
setupATLAS # set the ATLAS environment
lsetup git
git atlas init-workdir https://:@gitlab.cern.ch:8443/atlas/athena.git # initiate an empty working copy with "sparse checkout" from the HEAD
cd athena
git atlas addpkg VP1HEPVis VP1Base # add two packages
asetup master,r2017-05-07,Athena # set the ATLAS "nightly" development release; this will create the "CMakeLists.txt" file
cp Projects/WorkDir/package_filters_example.txt package_filters.txt
sed -i '/^-/ d' package_filters.txt # remove the last line of the "filters" file
sed -i '/^+ Control/ d' package_filters.txt # remove the example line
echo "+ graphics/VP1/VP1HEPVis" >> package_filters.txt # append this line to the "filters" file, to set the compilation of the added package
echo "+ graphics/VP1/VP1Base" >> package_filters.txt # append this line to the "filters" file, to set the compilation of the added package
echo "- .*" >> package_filters.txt # add back the last line, which was removed above
cd ../../build
cmake ../source/athena/ # configure the project

```