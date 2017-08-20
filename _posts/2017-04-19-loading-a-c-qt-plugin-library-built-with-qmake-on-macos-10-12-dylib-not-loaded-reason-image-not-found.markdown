---
layout: post
title: 'Loading a C++ Qt Plugin library built with QMake on macOS 10.12 - "dylib not
  loaded - reason: image not found"'
date: '2017-04-19 15:39:35'
---


Today I'm trying to compile one of my C++ applications on my newest Macbook Pro, which comes with macOS 10.12 ("Sierra") and where I installed the latest Xcode 8.3.

The application, which compiled and run correctly on my older Macbook Pro with Mac OSX 10.9 ("Mavericks"), does not compile anymore, out of the box. 

First I faced a linking error which puzzled me a lot, and I had to add a linker flag to make the newest Clang work (as I described [in another post](http://)).

And now I'm facing another problem. My C++ application uses the Qt Plugin mechanism to load custom plugin libraries at run time, using the lines of code here below:

```cpp
QPluginLoader * loader = new QPluginLoader(pluginAbsPath);
d->pluginfile_2_pluginloader[filename]=loader;
bool loadOk = loader->load();
```

Now, for some reasons, the plugin library cannot be loaded anymore...

I try to run the application binary, `vp1light`, but the program says the plugin cannot be loaded.

To get some more debug information, I set a couple of environment variables:

```bash
export QT_MESSAGE_PATTERN="[%{type}] %{appname} (%{file}:%{line}) - %{message}"
export QT_DEBUG_PLUGINS=1
```
In this way I get the following message, when trying to load the plugin library:

```bash
[debug] VP1 (unknown:0) - Cannot load library /Users/rbianchi/code_work_local/geometry_persistification/GeoModelPersistification/local/lib/libVP1GeometryPlugin.dylib: (dlopen(/Users/rbianchi/code_work_local/geometry_persistification/GeoModelPersistification/local/lib/libVP1GeometryPlugin.dylib, 133): Library not loaded: libCxxUtils.1.dylib
  Referenced from: /Users/rbianchi/code_work_local/geometry_persistification/GeoModelPersistification/local/lib/libVP1Utils.1.dylib
  Reason: image not found)
```

The library `VP1GeometryPlugin.dylib` is the library I want to load at runtime. Apparently the reason why it cannot be loaded is not the library itself but another library on which this library depends, as it can be inferred by the text of the long error message:

```bash
Library not loaded: libCxxUtils.1.dylib
[...]
Referenced from: [...] libVP1Utils.1.dylib
[...]
Reason: image not found
```

The `CxxUtils` package is part of my application. And it is used by the `VP1Utils.dylib` library, which, in turn, is used by the `VP1GeometryPlugin.dylib` library. 
Thus, at first, I verify that the `CxxUtils` library has been correctly compiled. And apparently it is.

So, in order to get some more information, I first try to run the `otool` utility on the executable:

```bash
otool -L vp1light
```

The command `otool -L` gives the list of the shared libraries used by the executable:

```bash
$ otool -L local/bin/vp1light
local/bin/vp1light:
	@rpath/libVP1Base.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	@rpath/libVP1HEPVis.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	@rpath/libVP1Gui.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	@rpath/libVP1UtilsBase.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	@rpath/QtGui.framework/Versions/5/QtGui (compatibility version 5.8.0, current version 5.8.0)
	@rpath/QtCore.framework/Versions/5/QtCore (compatibility version 5.8.0, current version 5.8.0)
	/System/Library/Frameworks/DiskArbitration.framework/Versions/A/DiskArbitration (compatibility version 1.0.0, current version 1.0.0)
	/System/Library/Frameworks/IOKit.framework/Versions/A/IOKit (compatibility version 1.0.0, current version 275.0.0)
	/System/Library/Frameworks/OpenGL.framework/Versions/A/OpenGL (compatibility version 1.0.0, current version 1.0.0)
	/System/Library/Frameworks/AGL.framework/Versions/A/AGL (compatibility version 1.0.0, current version 1.0.0)
	/usr/lib/libc++.1.dylib (compatibility version 1.0.0, current version 307.5.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1238.50.2)
```

The above output does not give any useful hint to solve the problem; but that's normal, because the problematic plugin library is loaded only at run-time, dynamically.

So, let's try to run the `otool` utility on the problematic plugin library itself:

```bash
$ otool -L local/lib/libVP1GeometryPlugin.dylib
local/lib/libVP1GeometryPlugin.dylib:
	libVP1GeometryPlugin.dylib (compatibility version 0.0.0, current version 0.0.0)
	@rpath/libVP1GuideLineSystems.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	@rpath/libVP1GeometrySystems.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	@rpath/libVP1Base.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	@rpath/QtWidgets.framework/Versions/5/QtWidgets (compatibility version 5.8.0, current version 5.8.0)
	@rpath/QtGui.framework/Versions/5/QtGui (compatibility version 5.8.0, current version 5.8.0)
	@rpath/QtCore.framework/Versions/5/QtCore (compatibility version 5.8.0, current version 5.8.0)
	/System/Library/Frameworks/DiskArbitration.framework/Versions/A/DiskArbitration (compatibility version 1.0.0, current version 1.0.0)
	/System/Library/Frameworks/IOKit.framework/Versions/A/IOKit (compatibility version 1.0.0, current version 275.0.0)
	/System/Library/Frameworks/OpenGL.framework/Versions/A/OpenGL (compatibility version 1.0.0, current version 1.0.0)
	/System/Library/Frameworks/AGL.framework/Versions/A/AGL (compatibility version 1.0.0, current version 1.0.0)
	/usr/lib/libc++.1.dylib (compatibility version 1.0.0, current version 307.5.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1238.50.2)
```

The `VP1Utils` library, which reference the `CxxUtils` that cannot be loaded, is not in the list of the shared libraries used by the `libVP1GeometryPlugin.dylib` library. But I know that the `VP1GeometrySystem` package, which is listed, uses it. So I run `otool -L` on that library:

```bash

```

And then, I run `otool -L` on the `VP1Utils` library as well:

```bash
$ otool -L local/lib/libVP1Utils.dylib
local/lib/libVP1Utils.dylib:
	@rpath/libVP1Utils.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	/usr/local/lib/libCoin.80.dylib (compatibility version 81.0.0, current version 81.0.0)
	@rpath/libVP1HEPVis.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	@rpath/libVP1Base.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	libCxxUtils.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	@rpath/libGeoModelKernel.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	@rpath/QtGui.framework/Versions/5/QtGui (compatibility version 5.8.0, current version 5.8.0)
	@rpath/QtCore.framework/Versions/5/QtCore (compatibility version 5.8.0, current version 5.8.0)
[...]
```

Here I finally see something...

The `CxxUtils` problematic library is listed; and it is listed without the `@rpath` prefix.

So, from that we can infer that the build of the `CxxUtils` is not made correctly for modern Mac platforms.

I now fix that by adding this line to the `.pro` file of the `CxxUtils` package:

```
mac {
  QMAKE_LFLAGS_SONAME = -Wl,-install_name,@rpath/
}
```

This makes the dynamic library discoverable by the executable or the other libraries which link it.
Apparently on newest Mac platforms this requirement is more strict.

Now, after having compiled the `CxxUtils` and the `VP1Utils` library again, if I run the `otool -L` command on the new `VP1Utils` library, I see that the `CxxUtils` library is now listed with the correct `@rpath` prefix:

```bash
$ otool -L local/lib/libVP1Utils.dylib
local/lib/libVP1Utils.dylib:
	@rpath/libVP1Utils.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	/usr/local/lib/libCoin.80.dylib (compatibility version 81.0.0, current version 81.0.0)
	@rpath/libVP1HEPVis.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	@rpath/libVP1Base.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	@rpath/libCxxUtils.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	@rpath/libGeoModelKernel.1.dylib (compatibility version 1.0.0, current version 1.0.0)
	@rpath/QtGui.framework/Versions/5/QtGui (compatibility version 5.8.0, current version 5.8.0)
	@rpath/QtCore.framework/Versions/5/QtCore (compatibility version 5.8.0, current version 5.8.0)
[...]
```

And now, if I run my application... **the plugin is correctly loaded again!**


