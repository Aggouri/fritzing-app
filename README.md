# Fritzing

This is a fork making the compilation work under macOS 13.5.2.

## Compilation instructions

### Clone this repo

Define the directory where you will be building the application and its dependencies.

```sh
export STAGING_AREA=...
mkdir -p ${STAGING_AREA}
```

Clone this fork in `${STAGING_AREA}`:

```sh
cd ${STAGING_AREA}
gh repo clone Aggouri/fritzing-app
```

### Qt

Install qt 5.15.2 using the [online installer]((https://www.qt.io/download-qt-installer-oss)). You can skip installing Android and iOS components.

Make a note of the installation path, e.g. `/users/Alex/Qt/5.12.5`. Let's call this path  `<QT_PATH>`. 

Version 5.12.5 has a [bug](https://codereview.qt-project.org/c/qt/qtbase/+/503172) which needs to be fixed manually. Replace the contents of file `<QT_PATH>/clang_64/mkspecs/features/toolchain.prf` with the contents of the file in [./qt-bugfix/toolchain.prf](./qt-bugfix/toolchain.prf).

### Dependencies

Download the following:

- [boost 1.83.0](https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source/boost_1_83_0.tar.gz) from [https://boost.org](https://boost.org).
- [libgit2 0.28.5](https://github.com/libgit2/libgit2/archive/refs/tags/v0.28.5.tar.gz) from [https://github.com/libgit2/libgit2](https://github.com/libgit2/libgit2/releases/tag/v0.28.5).
- [ngspice 40](https://sourceforge.net/projects/ngspice/files/ng-spice-rework/40/ngspice-40.tar.gz/download) from [https://ngspice.sourceforge.io](https://ngspice.sourceforge.io/download.html).
- [quazip 1.4](https://github.com/stachenov/quazip/archive/refs/tags/v1.4.tar.gz) from [https://github.com/stachenov/quazip](https://github.com/stachenov/quazip/releases/tag/v1.4).
- [svgpp 1.3.0](https://github.com/svgpp/svgpp/archive/refs/tags/v1.3.0.tar.gz) from [https://github.com/svgpp/svgpp](https://github.com/svgpp/svgpp/releases/tag/v1.3.0).

Extract the dowloaded file contents in `${STAGING_AREA}`, making sure the directories match the following names exactly:

```sh
.
├── boost_1_83_0
├── fritzing-app
├── libgit2
├── ngspice-40
├── quazip_qt5
└── svgpp-1.3.0
```

Build `libgit2`:

```sh
cd ${STAGING_AREA}/libgit2
mkdir build
cd build
cmake -DBUILD_SHARED_LIBS=OFF ..
cmake --build .
```

> If your system doesn't have `libssh2`, install it:
> 
> ```sh
> brew install libssh2
> ```

Ensure the path to the `libssh2` library is correct in [./pri/libgit2detect.pri](./pri/libgit2detect.pri) (Look for `LIBS += /usr/local/Cellar/libssh2/1.11.0_1/lib/libssh2.1.dylib`).

Build `quazip`:

```sh
cd ${STAGING_AREA}/quazip_qt5
# replace QT_PATH with the actual path, as defined earlier
export Qt5_DIR=<QT_PATH>/clang_64
cmake -S . -B build -D QUAZIP_QT_MAJOR_VERSION=5 -D CMAKE_INSTALL_PREFIX=.
cmake --build build --target install
```

### Edit build script

Edit `QTBIN` in [./tools/deploy_fritzing_mac.sh](./tools/deploy_fritzing_mac.sh):

Make it point to the correct `QT_PATH` value, as defined earlier.

```
QTBIN=<QT_PATH>/clang_64/bin
```

### Run the build script

Run the script and hope for the best:

```sh
cd ${STAGING_AREA}/fritzing-app
./deploy_fritzing_mac.sh
```

---

|Branch|Badge|
|------|-----|
|master|[![Build Status](https://travis-ci.org/fritzing/fritzing-app.svg?branch=master)](https://travis-ci.org/fritzing/fritzing-app)|
|develop|[![Build Status](https://travis-ci.org/fritzing/fritzing-app.svg?branch=develop)](https://travis-ci.org/fritzing/fritzing-app)|

The Fritzing application is an Electronic Design Automation software with a low entry barrier, suited for the needs of makers and hobbyists. It offers a unique real-life "breadboard" view, and a parts library with many commonly used high-level components. Fritzing makes it very easy to communicate about circuits, as well as to turn them into PCB layouts ready for production. It is particularly popular among Arduino and Raspberry Pi users, and is widely used in education and creative tinkering.

* For more information on Fritzing and its related activities, visit [http://fritzing.org](http://fritzing.org). There you can also [download](http://fritzing.org/download) the latest releases for all platforms and get help on getting started.

* To report a problem or suggest improvements, use the [issue tracker](https://github.com/fritzing/fritzing-app/issues) or the [user forum](http://forum.fritzing.org).
Please provide steps, what operating system you are on, including the version. Add screenshots or copies of error messages, describe what behavior you saw and what you expected.

* If you would like to help with the development, please take a look at those labels:
  * [![label: easy start][~easy start]](https://github.com/fritzing/fritzing-app/labels/easy%20start)
  * [![label: challenging start][~challenging start]](https://github.com/fritzing/fritzing-app/labels/challenging%20start)

Some of those don't need C++ skills, like reproducing an issue on a certain platform, or verifying translations of languages we don't speak. If there is something for you, you might want to check the [developer instructions](https://github.com/fritzing/fritzing-app/wiki) next. This includes information about how to compile and run the Fritzing app.

## Project Structure

* **help** - End-user documentation included with the app. 

* **parts** - All the part definitions, including meta data (.fzp) and graphics (.svg), as well as some utility tools. They are kept in a separate repository at [https://github.com/fritzing/fritzing-parts](http://github.com/fritzing/fritzing-parts) and only linked from here.

* **pri** - Submodule definitions for Qt

* **resources** - Binaries and definitions that are supposed not to be touched by users, such as fonts, images, special parts, etc.

* **sketches** - Example circuits/sketches shipped with the application

* **src** - Application logic!

* **tools** - Utility tools for making releases, converting parts, etc.

* **translations** - Language translations

## Credits

The Fritzing app was maintained by the Friends-of-Fritzing e.V., a non-profit foundation based in Berlin, Germany. The project has grown out of a state-funded research project at the [Interaction Design Lab](http://idl.fh-potsdam.de) at [Potsdam University of Applied Sciences](http://fh-potsdam.de). 

The founding team consists of Prof. Reto Wettach, André Knörig, Jonathan Cohen, and Stefan Hermann. Many [fantastic people](http://fritzing.org/about/people/) have contributed to it over the years.

Since 2019, the project is maintained by Kjell Morgenstern, with great support from Peter Van Epp, André Knörig, and AISLER.

The Fritzing app is written on top of the [Qt cross-platform framework](http://qt-project.org).

## Licensing

The source code of Fritzing is under GNU GPL v3, the documentation and part designs under Creative Commons Attribution-ShareALike 3.0 Unported. The full texts of these licenses are shipped with this download.

This means that you can create your own variation of Fritzing, as long as you credit us and also publish it under GPL. Similarly, you may re-publish our documentation, as long as you credit us, and publish it under the same
license. You may publish circuits and diagrams that you create with Fritzing and that use our graphics, again as long as you credit us, and
publish your works under the same license.  A credit can be as simple as "this image was created with Fritzing."

Lookup [our FAQs](http://fritzing.org/faq/) for more details on licensing.

[~help wanted]: https://img.shields.io/badge/-help%20wanted-%23159818
[~easy start]: https://img.shields.io/badge/-easy%20start-%2333AAFF
[~challenging start]: https://img.shields.io/badge/-challenging%20start-%235500EE
