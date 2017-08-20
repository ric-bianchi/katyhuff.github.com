---
layout: post
title: 'CMake and Qt: undefined reference to QWidget::mapTo()'
---

I'm building a package against a custom local installation of Qt5, setting it explicitely in CMake:

```cmake
# requiring the Qt5 5.8.0 built locally on AFS
set( QT580_ROOT /local/x86_64-slc6-gcc62-opt )
set( ENV(CMAKE_PREFIX_PATH) ${QT580_ROOT} )
find_package( Qt5 5.8.0 REQUIRED COMPONENTS Core Widgets OpenGL Gui Network PrintSupport  HINTS ${QT580_ROOT} )
```

But then I was keeping getting a strange error:

```bash
CMakeFiles/VP1Base.dir/src/IVP13DChannelWidget.cxx.o: In function `IVP13DChannelWidget::getSnapshot(bool, int, bool)':
/code_local/source/athena/graphics/VP1/VP1Base/src/IVP13DChannelWidget.cxx:174: undefined reference to `QWidget::mapTo(QWidget*, QPoint const&) const'
collect2: error: ld returned 1 exit status
```


```bash
Scanning dependencies of target VP1Base
[ 23%] Building CXX object graphics/VP1/VP1Base/CMakeFiles/VP1Base.dir/src/IVP13DChannelWidget.cxx.o
/code_local/source/athena/graphics/VP1/VP1Base/src/IVP13DChannelWidget.cxx: In member function 'virtual QPixmap IVP13DChannelWidget::getSnapshot(bool, int, bool)':
/code_local/source/athena/graphics/VP1/VP1Base/src/IVP13DChannelWidget.cxx:174:36: error: invalid conversion from 'const QWidget*' to 'QWidget*' [-fpermissive]
   QPoint pos = ra_w->mapTo( qq, pp );
                                    ^
In file included from /usr/include/QtGui/QWidget:1:0,
                 from /code_local/source/athena/graphics/VP1/VP1Base/VP1Base/IVP1ChannelWidget.h:22,
                 from /code_local/source/athena/graphics/VP1/VP1Base/VP1Base/IVP13DChannelWidget.h:18,
                 from /code_local/source/athena/graphics/VP1/VP1Base/src/IVP13DChannelWidget.cxx:15:
/usr/include/QtGui/qwidget.h:313:12: note:   initializing argument 1 of 'QPoint QWidget::mapTo(QWidget*, const QPoint&) const'
     QPoint mapTo(QWidget *, const QPoint &) const;
            ^~~~~
make[2]: *** [graphics/VP1/VP1Base/CMakeFiles/VP1Base.dir/src/IVP13DChannelWidget.cxx.o] Error 1
make[1]: *** [graphics/VP1/VP1Base/CMakeFiles/VP1Base.dir/all] Error 2
make: *** [all] Error 2
```

Guessed what?

Look at the `mapTo` function definition the linker is looking at... It's not that one from my local custom installation, but that one which comes with my operating system, which turns out to be Qt 4.8!

That's the problem! I set CMake to compile against my custom build of Qt 5.8, but somehow the linker is looking at the QWidget header file shipped with my system Qt 4.8.

In fact, here below you can see the difference in the definition of the
`mapTo` function in the two versions:

```cpp
QPoint mapTo(QWidget *, const QPoint &) const;
```

```cpp
QPoint mapTo(const QWidget *, const QPoint &) const;
```

I fixed the problem explicitly adding the `include` folder of my custom local build to the CMake `add_library` statement, as here below:

```cmake
atlas_add_library( VP1Base VP1Base/*.h src/*.cxx src/*.qrc
   PUBLIC_HEADERS VP1Base
   INCLUDE_DIRS ${QT580_ROOT}
   PRIVATE_INCLUDE_DIRS ${CMAKE_CURRENT_BINARY_DIR}
   LINK_LIBRARIES Qt5::Core Qt5::Gui Qt5::PrintSupport Qt5::Widgets 
   PRIVATE_LINK_LIBRARIES Qt5::OpenGL )
```
