#This file describes how to build Shashlik, the simulated Android environment.



#Getting the Sources

Shashlik uses Dalvik, the Android runtime interpreter, to run the
applications.  In addition to Dalvik itself we need to have a number of
dependencies from the Google Android source tree to be able to build
it. 

Dalvik is normally built inside the full Android source tree and we want to
minimize the dependency tree when building on a normal Linux system. Here is
the list of sources that are necessary to build Shashlik. For each component
below you should be able to just cut and paste the command lines into a shell.

The source trees should be placed next to each other in a common subdirectory,
e.g. $HOME/SHASHLIK or something similar.  In the rest of this file we will
assume that you are using this location.  If not, you will have to adjust the
instructions below accordingly.
```
  cd
  mkdir SHASHLIK
  cd SHASHLIK
```
Create the main directory for the sources. We recommend using a separate
directory for the source tree because you will also have the install directory
at this level (see below under Building the sources).
```
  mkdir src
  cd src
```
Then proceed to download the git and svn repositories as follows.  


1. The 9 repositories from the Shashlik project on github.

Shashlik has 8 repos from the Google Android sources copied into Github. These
8 repos are Dalvik, Android Core, Android Libcore, Frameworks Native, Frameworks Base,
libnativehelper, telephony and skia.
The changes in these repositories will be kept as small as possible so that it will
be easier to upgrade to a later version later. The main code written for Shashlik
is created in the 9th repository of the project named (surprise!) shaslik.

The main page of the project is https://github.com/shashlik. Here you will
find links to all 9 repositories. Since the shashlik repos are private you
will need to give your username and password to clone them.
```
  git clone https://github.com/shashlik/shashlik.git
  git clone --branch shashlik-kitkat https://github.com/shashlik/dalvik.git
  git clone --branch shashlik-kitkat https://github.com/shashlik/android-core.git
  git clone --branch shashlik-kitkat https://github.com/shashlik/android-libcore.git
  git clone --branch shashlik-kitkat https://github.com/shashlik/android-frameworks-native.git
  git clone --branch shashlik-kitkat https://github.com/shashlik/android-frameworks-base.git
  git clone --branch shashlik-kitkat https://github.com/shashlik/android-libnativehelper.git
  git clone --branch shashlik-kitkat https://github.com/shashlik/android-telephony.git
  git clone --branch shashlik-kitkat https://github.com/shashlik/android-skia.git
```
2. Safe-IOP

Safe IOP is also a Google library which is described like this on the project
home page at https://code.google.com/p/safe-iop/:

    "This library provides a collection of (macro-based) functions for
    performing safe integer operations across platform and architecture with a
    straightforward API."

This library is kept in subversion. To check it out, do the following:
```
  svn checkout http://safe-iop.googlecode.com/svn/trunk/ safe-iop
  cd safe-iop
  svn switch http://safe-iop.googlecode.com/svn/tags/r0.3.1
  cd ..
```
We will use a specific release, in this case 0.3.1, to get a stable platform.
This is the reason for the svn switch.


3. Bionic

Bionic is a derivation of the BSD's standard C library code that was
originally developed by Google for their Android[2] operating system
(Wikipedia). It is a stripped-down version of libc that contains exactly what
is needed on Android. It is necessary to build Dalvik, although there is a
project to port Dalvik to GNU libc.

There is a project called dvk (https://code.google.com/p/dvk/) whose goal is
to port dalvik to other CPU architectures and away from needing bionic but we
have not gone down that route.
```
  git clone https://android.googlesource.com/platform/bionic
```
We use the kitkat tag for this one, meaning you need to run:
```
  cd bionic
  git checkout android-4.4.4_r2.0.1
  cd ..
```
Which will leave you with a detached head


4. OpenSSL

openssl is the premiere secure socket layer library used by free software projects,
and android has made certain modifications to it which makes it difficult to simply
use the one provided by most linux distributions. So, we use theit modified version.
```
  git clone https://android.googlesource.com/platform/external/openssl
  cd openssl
  git checkout android-4.4.4_r2.0.1
  cd ..
```
5. ext libraries

A set of java libraries used by android and packed together into one jar.
```
  git clone https://android.googlesource.com/platform/external/nist-sip
  git clone https://android.googlesource.com/platform/external/apache-http
  git clone https://android.googlesource.com/platform/external/tagsoup
  git clone https://android.googlesource.com/platform/external/libphonenumber
  git clone https://android.googlesource.com/platform/external/okhttp
  git clone https://android.googlesource.com/platform/external/bouncycastle
```
for all of the above:
```
  cd nist-sip
  git checkout android-4.4.4_r2.0.1
  cd ..
  cd apache-http
  git checkout android-4.4.4_r2.0.1
  cd ..
  cd tagsoup
  git checkout android-4.4.4_r2.0.1
  cd ..
  cd libphonenumber
  git checkout android-4.4.4_r2.0.1
  cd ..
  cd okhttp
  git checkout android-4.4.4_r2.0.1
  cd ..
  cd bouncycastle
  git checkout android-4.4.4_r2.0.1
  cd ..
```
6. jni requirements
```
  git clone https://android.googlesource.com/platform/frameworks/opt/net/voip
  git clone https://android.googlesource.com/platform/external/liblzf
  git clone https://android.googlesource.com/platform/external/protobuf
  git clone https://android.googlesource.com/platform/external/sqlite
  git clone https://android.googlesource.com/platform/frameworks/opt/emoji android-emoji
  git clone https://android.googlesource.com/platform/build android-build
  cd voip
  git checkout android-4.4.4_r2.0.1
  cd ..
  cd liblzf
  git checkout android-4.4.4_r2.0.1
  cd ..
  cd protobuf
  git checkout android-4.4.4_r2.0.1
  cd ..
  cd sqlite
  git checkout android-4.4.4_r2.0.1
  cd ..
  cd android-emoji
  git checkout android-4.4.4_r2.0.1
  cd ..
  cd android-build
  git checkout android-4.4.4_r2.0.1
  cd ..
  ```
  We also require the Android 5.1 version of libhardware and master head of android
  core (for libbase)
  ```
  git clone https://android.googlesource.com/platform/hardware/libhardware
  git clone https://android.googlesource.com/platform/system/core core-head
  cd libhardware
  git checkout android-5.1.0_r1
  cd ..
```
  The following libraries are not currently used, but will be required once RenderScript
  support is functional.
  ```
  git clone https://android.googlesource.com/platform/frameworks/compile/libbcc
  git clone https://android.googlesource.com/platform/frameworks/rs
  cd libbcc
  git checkout android-4.4.4_r2.0.1
  cd ..
  cd rs
  git checkout android-4.4.4_r2.0.1
  cd ..
```
7. Graphics assistance libraries

These libraries are an attempt to get SurfaceFlinger to work on normal Linux, and so
we piggyback on that work. Minor changes have been made to the drm_gralloc module, and
until those are upstreamed or otherwise fixed, we keep a local clone of it.
```
  git clone https://android.googlesource.com/platform/hardware/libhardware_legacy
  git clone git://anongit.freedesktop.org/mesa/mesa
  git clone https://github.com/shashlik/drm_gralloc.git
  cd drm_gralloc
  git checkout lollipop-x86
  cd ..
```
The following are from https://wiki.linaro.org/BenjaminGaignard/HWComposer_DRM and
they are a part of an attempt to get things running on a full blood linux as well
which of course aligns very well with our efforts.
```
  git clone git://git.linaro.org/people/benjamin.gaignard/gralloc.git
  git clone https://github.com/shashlik/hwcomposer-linaro.git hwcomposer
```
8. Gentroid

This code is a clone and modification of the gentroid code, used in the leinir2 branch
to build jni
```
  git clone https://github.com/shashlik/gentroid-frameworks-base.git
```
9. JarJar

This is a tool used for doing modifications to jar files, such as relocating packages
to other sub-domains (for example, org.conscrypt.* becomes com.android.conscrypt...).

Leave this file in the base source directory so the build system can find it. As google
code is shutting down, this may need to relocate at some point (other mirrors exist,
but this is the canonical release address for the file)

```
wget https://jarjar.googlecode.com/files/jarjar-1.4.jar
```
#Building the Sources

The goal of Shashlik is to create a stand-alone Android runtime
environment. This basically means Dalvik with a number of support features
such as emulated sensors and a screen.

Dalvik itself needs a number of support classes and libraries from other parts
of the Android sources. Unfortunately the dependencies themselves drag in more
dependencies and so on until most of Android is present. It turns out that
Dalvik itself does not use that many classes and functions in the dependency
libraries so by selecting to build only those classes that are actually needed
we can keep down the number of repositories that we need to check out.

In this description we assume that you have downloaded the sources above and
that they are all in ```$HOME/shashlik```. 

At this time the instructions assume that you are building on a standard Linux
desktop system with an installed software development kit. Refer to your
distribution on how to install this. As far as we know there are no
distribution specific problems or peculiarities.


Prerequisites
-------------

Aside from using a normal Linux development system (make, gcc, etc) you need
a variety of components that might not be on everybodys computers:
 - CMake, version 2.8.8 minimum
 - Qt 5.3
 - KDE Frameworks 5 (KF5), specifically the following modules:
   I18n WindowSystem Service Package Declarative Plasma PlasmaQuick Wayland
 - Kernel headers
 - Java SDK version 6 (or newer, we're just forcing target/source)
 - flex and bison

 - 32 bit development headers for all the following libraries, used by the android VM
   Fontconfig
   Freetype
   giflib
   libjpeg62
   libwebp
   libpng - version 1.2
   harfbuzz
   glu-devel
   libcap
   zlib-devel
   ffi
   icuuc
   icui18n
   expat
   crypto
   openssl
   Qt5Core
   GLESv2
   GLESv1_CM
   selinux
   Wayland EGL
   Qt 5.3 modules: Core, Gui, Widgets, X11Extras
   Note! All the libraries above must be 32bit versions, as they are used by the
   (always) 32 bit android runtime

   for openSuse, you can run the following commands to get what you need:
   ```
   sudo zypper install -t pattern devel_c_cpp devel_java devel_qt5 devel_kde_frameworks
   sudo zypper install libharfbuzz0-32bit libharfbuzz-icu0-32bit glibc-devel-32bit \
        libstdc++-devel-32bit gcc-32bit gcc-c++-32bit libwebp-devel libwebpdecoder1-32bit \
        libwebpdemux1-32bit libwebpmux1-32bit fontconfig-devel-32bit zlib-devel-32bit \
        freetype2-devel-32bit glu-devel-32bit giflib-devel-32bit libpng12-devel-32bit \
        libpng12-compat-devel-32bit  libjpeg62-32bit libjpeg62-devel-32bit \
        libicu-devel-32bit libdrm-devel-32bit libcap-devel \
        libQt5Core-devel-32bit libffi48-devel-32bit libexpat-devel-32bit libopenssl-devel-32bit \
        java-1_7_0-openjdk-devel jarjar libselinux-devel \
        libQt5Widgets-devel-32bit  libqt5-qtx11extras-devel-32bit \
        libwayland-egl-devel-32bit kwayland-devel-32bit
```
This instruction will from here on assume that you have these installed
correctly.  If not, you will have to install them (probably build yourself)
using their respective installation instructions.

NOTE: Even after installing Qt5.3 and KF5 correctly for normal usage you will
      still have to add a specific environment variable to be able to use them
      for development using CMake:

        ```export CMAKE_PREFIX_PATH=$KF5:$CMAKE_PREFIX_PATH```

We also suggest to create an environment variable with the path of the
Shashlik top directory.

  ```export SHASHLIK=$HOME/SHASHLIK```

In the rest of this document we shall use this variable in our examples.


Branches
--------

We are trying to minimize the changes in each component as much as
possible. However some changes are necessary and cannot be avoided. So for
each dependency that does need to be changed, we have created a branch called
shashlik-kitkat. 

Some of the components have release tags that we are using so that we can have
a stable build.  Here is a list of the branches and tags that we are using. If
you have checked out the repositories using the commands given above they
should be on the correct branch already.

* shashlik:                   branch master
* dalvik:                     branch shashlik-kitkat
* android-core:               branch shashlik-kitkat
* android-libcore:            branch shashlik-kitkat
* android-frameworks-native:  branch shashlik-kitkat
* android-frameworks-base:    branch shashlik-kitkat

* frameworks-native           branch kitkat-release
* safe-iop:                   tag http://safe-iop.googlecode.com/svn/tags/r0.3.1
* libnativehelper:            branch kitkat-release
* bionic:                     tag android-4.4.4_r2.0.1
* skia:                       tag android-4.4.4_r2.0.1
* openssl:                    tag android-4.4.4_r2.0.1

As you can see above, the repo shashlik itself is the one where we try to put
all of our changes so that should be on master branch unless you are working
on some new feature in a feature branch.


Building
--------

Building Shashlik is slightly more complicated than building a normal source
tree. This is because we are taking sources from the Android source tree and
build them in a way that they are not designed for.

You can install anywhere you like, basically, but we highly recommend that you
do so by creating a SHASHLIK/install directory. Note that the install directory
should be _outside_ the shashlik repo source tree but under the SHASHLIK top
directory.

  ```cd $SHASHLIK
  mkdir install```

For building, the procedure is in itself similar to how you normally compile
any other cmake based project, however the directory structure is vital. The
build system expects the location of the sources to be as suggested in Getting
The Sources above. In other words, you should have a directory structure
similar to the following tree. Those marked with * are directories you should
be creating, all the others are git clones (and that one svn checkout):

+ SHASHLIK *
  + install *
  + src *
    + android-core
    + android-frameworks-native
    + android-frameworks-base
    + android-libcore
    + bionic
    + dalvik
    + libnativehelper
    + safe-iop
    + (etc, other checkouts)
    + shashlik
      + build *


Lastly you will have to build the shashlik sources that depend on the selected
Android libraries that were created in the builddeps directory. This is done
in the $SHASHLIK/src/shashlik/build/ directory and it is really easy:
```
  cd $SHASHLIK/src/shashlik/build
  cmake .. -DCMAKE_INSTALL_PREFIX=$SHASHLIK/install
  make install
```

The main control for the shashlik android runtime is shashlik-controller, the
only binary installed outside of the android root. Run this command with --help
to see what options you've got. This binary currently makes assumptions regarding
the setup of your system. These assumptions will be removed at a later date.

To run an application, run as follows:
```
shashlik-controller --start
shashlik-controller --launchapk=/path/to/apk-file.apk
```
