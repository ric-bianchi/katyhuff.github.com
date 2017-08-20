---
layout: post
title: compiling grav-sim on Mac OS X Mavericks
date: '2016-04-01 21:52:57'
---

#### Prepare a new Makefile for Mac OS X

Grav-sim comes with Makefiles for Unix and Windows. Let us now prepare one for Mac. Copy the file for the Unix system:
```
cp Makefile.inc.unix-gcc Makefile.inc.mac-gcc
```
Then modify the new `Makefile.inc.mac-gcc` replacing these lines:

```
CCFLAGS=-c -O3 -DOMP -fopenmp
LDFLAGS=
STDLIBS=-lgomp -lpthread -lglut -lstdc++
```
with these ones:
```
CCFLAGS=-c -O3 -DOMP -fopenmp -framework GLUT -framework OpenGL
LDFLAGS=-framework OpenGL -framework GLUT
STDLIBS=-lgomp -lpthread -lstdc++
```
(if you still get the error `ld: library not found for -lglut`, please check that you have removed the string `-lglut` from STDLIBS)

And then copy the template file to the actual file used by `Make`:

```
cp Makefile.inc.mac-gcc Makefile.inc
```

For more info about GLUT on Mac OS X:

 * http://web.eecs.umich.edu/~sugih/courses/eecs487/glut-howto/#mac
 * http://iihm.imag.fr/blanch/software/glut-macosx/
 * https://developer.apple.com/library/mac/qa/qa1613/_index.html

----

#### Install an updated GCC with OpenMP support

Let us try to compile Grav-sim:
````
make
```

Compiling Grav-sim on Mac OS X (I'm using 10.9) you can get this error:

```bash
gcc -c -O3 -DOMP -fopenmp -DFASTSIM  -o analyser.o ../../analyser.cpp
clang: warning: argument unused during compilation: '-fopenmp'
In file included from ../../analyser.cpp:17:
../../acs.h:55:10: fatal error: 'omp.h' file not found
#include <omp.h>
         ^
1 error generated.
make[3]: *** [analyser.o] Error 1
make[2]: *** [fastsim] Error 2
make[1]: *** [fastsim] Error 2
make: *** [fastsim] Error 2
```

This is because the GCC installed on Mac OS X (Mavericks) does not support OpenMP by default.

So let us install an updated GCC with OpenMP support, the default one is installed without OpenMP support:

```
brew reinstall gcc --without-multilib
```

it will install an updated GCC (5.3 at the time of writing) with OpenMP support (that's what the flag `--without-multilib` does) in parallel with the default GCC that is already installed on the system. 

If you get this error:
```
Error: Could not symlink share/info/gmp.info
/usr/local/share/info is not writable.
```

you have to change ownership to the folder /usr/local/ otherwise Brew cannot create the symlinks. Change it by typing:

```
sudo chown -R $USER:admin /usr/local
```

If everything goes fine, at the end of the process the new GCC is installed on your system.
To avoid clashes between the two, Brew install the new GCC as symlinked from the new command (or something similar, depending on the version):

```
/usr/local/bin/gcc-5
```

To use the new GCC in compilation, we have to modify the Makefile. Open the file `Makefile.inc.mac-gcc` and replace these lines:

```
CC=gcc
LD=gcc
```
with these ones:
```
CC=gcc-5
LD=gcc-5
```
in this way the new version of GCC will be used. Afetr the changes, be sure to copy the Makefile template to the actual Makefile again:

```
cp Makefile.inc.mac-gcc Makefile.inc
```
and then let us try to compile Grav-sim again:

```
make clean
make
```

----

#### Fixing compilation errors:

If you get this error:

```
gcc-5 -c -O3 -DOMP -fopenmp -DGRAVSIM  -o analyser.o ../../analyser.cpp
In file included from ../../acs.h:89:0,
                 from ../../analyser.cpp:17:
../../body.h: In function 'void GravSim::direct_grav(PARTICLE1*, const PARTICLE2*, const GravSim::GravArgs&, const GravSim::Vector&, const Real&, const Real&)':
../../body.h:237:3: error: 'direct_ctsq' was not declared in this scope
   direct_ctsq<PARTICLE1, PARTICLE2>(particle1, particle2, d, rr);
   ^
../../body.h:237:24: error: expected primary-expression before ',' token
   direct_ctsq<PARTICLE1, PARTICLE2>(particle1, particle2, d, rr);
                        ^
../../body.h:237:35: error: expected primary-expression before '>' token
   direct_ctsq<PARTICLE1, PARTICLE2>(particle1, particle2, d, rr);
                                   ^
../../body.h: In function 'void GravSim::pairwise_grav(PARTICLE1*, PARTICLE2*, const GravSim::GravArgs&, const GravSim::Vector&, const Real&, const Real&)':
../../body.h:309:3: error: 'pairwise_ctsq' was not declared in this scope
   pairwise_ctsq<PARTICLE1, PARTICLE2>(particle1, particle2, d, rr);
   ^
../../body.h:309:26: error: expected primary-expression before ',' token
   pairwise_ctsq<PARTICLE1, PARTICLE2>(particle1, particle2, d, rr);
                          ^
../../body.h:309:37: error: expected primary-expression before '>' token
   pairwise_ctsq<PARTICLE1, PARTICLE2>(particle1, particle2, d, rr);
                                     ^
make[3]: *** [analyser.o] Error 1
make[2]: *** [gravsim] Error 2
make[1]: *** [gravsim] Error 2
make: *** [gravsim] Error 2
```

modify the place of the `direct_ctsq` function in the `acslib/body.h`, from the bottom of the file to the beginning of the file.

Also, the location of the GLUT header files on Mac OS X are not the same as on Linux, so let us modify the code of the file `glutapp/main.cpp`, replacing this line:

```
#include <GL/glut.h>
```

with these ones:

```c++
#if defined(__APPLE__)
  #include <GLUT/glut.h>
#else
  #include <GL/glut.h>
#endif
```
(Source: http://stackoverflow.com/a/10866413/320369)

Now let us try to compile it again:

```
make
```
----

#### Missing GNU Gengetopt package 

If you now get this error:

```
gengetopt: No such file or directory
```
You have to install the [GNU Gengetopt package](https://www.gnu.org/software/gengetopt/gengetopt.html), a tool to write command line option parsing code for C programs:

```
brew install gengetopt
```

and then, as usual:

```
make clean
make
```
----
#### DONE!

If you don't get other errors, you should now have Grav-sim compiled on your system!






