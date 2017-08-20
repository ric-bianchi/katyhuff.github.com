---
layout: post
title: tools to debug C++ programs
---


pb-d-128-141-134-164:src rbianchi$ otool -L path/to/executable

it gives all the libraries used by the output executable

which will display where the executable is expecting those libraries to be.

If the path to those need changing, use the install_name_tool command, which allows you to set the path to the libraries.

source: http://stackoverflow.com/a/17704255/320369


then there is the tool Joe pointed me out:

c++filt

