---
layout: post
title: "makeinfo not found! Installing 'texinfo' on SLC6"
---

While compiling the ATLAS Externals packages on a custom SLC6 machine, I git the error `makeinfo: command not found` error from the "Gdb" package:

```bash
installing zh_CN.gmo as /home/user/dev/buildax/External/Gdb/CMakeFiles/GdbBuild/share/locale/zh_CN/LC_MESSAGES/opcodes.mo
config.status: creating config.h
config.status: config.h is unchanged
config.status: executing default commands
config.status: executing depdir commands
/home/user/dev/buildax/src/Gdb/missing: line 81: makeinfo: command not found
WARNING: 'makeinfo' is missing on your system.
         You should only need it if you modified a '.texi' file, or
         any other file indirectly affecting the aspect of the manual.
         You might want to install the Texinfo package:
         <http://www.gnu.org/software/texinfo/>
         The spurious makeinfo call might also be the consequence of
         using a buggy 'make' (AIX, DU, IRIX), in which case you might
         want to install GNU make:
         <http://www.gnu.org/software/make/>
make[8]: *** [gdb.info] Error 127
make[7]: *** [subdir_do] Error 1
make[6]: *** [install-only] Error 2
make[5]: *** [install] Error 2
make[4]: *** [install-gdb] Error 2
make[3]: *** [install] Error 2
make[2]: *** [src/Gdb-stamp/Gdb-build] Error 2
make[1]: *** [External/Gdb/CMakeFiles/Gdb.dir/all] Error 2
make[1]: *** Waiting for unfinished jobs....
```

`makeinfo` is part of the `texinfo` package, and it turned out that on my custom SLC6 machine the package `texinfo` was not installed. It is, instead, installed by defalut on the CERN LXPLUS machines.

Thus, I installed it with `yum`:

```bash
sudo yum install texinfo
```

Testing after installation:

```bash
$ which makeinfo
/usr/bin/makeinfo
```
