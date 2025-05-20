<!---
{
  "id": "abe720ad-32b4-4303-9b61-5875f592c05c",
  "depends_on": [],
  "author": "Stephan Bökelmann",
  "first_used": "2025-05-16",
  "keywords": ["CopperSpice", "KitchenSink", "CMake", "C++", "GUI", "Build Process"]
}
--->

# Building the CopperSpice KitchenSink Demo

> In this exercise you will learn how to build the CopperSpice KitchenSink demo application from source. Furthermore, we will explore how to configure the build environment using CMake and Ninja, and understand the deployment process of a C++ GUI application.

---

## Introduction

The CopperSpice KitchenSink demo is a comprehensive application that showcases the capabilities of the CopperSpice libraries. It combines multiple demos into a single executable, demonstrating various features such as audio/video playback, networking, widgets, and graphics. Building this application from source provides valuable insights into setting up a C++ development environment, configuring build systems, and deploying GUI applications.

CopperSpice is a set of C++ libraries designed for developing cross-platform applications. It is a modern fork of the Qt framework, leveraging C++17 features and using CMake as its build system. The KitchenSink demo serves as an excellent starting point for developers to familiarize themselves with CopperSpice's architecture and functionalities.

In this exercise, we will guide you through the process of building the KitchenSink application. This includes setting up the necessary tools, configuring the build environment, compiling the source code, and running the application. By the end of this exercise, you will have a working knowledge of building and deploying a CopperSpice application.

### Further Readings and Other Sources

* [Video-Description of Building the Project](https://youtu.be/QQ8OR9bNru4?si=73G0ZhGVwa8MSaHm)
* [CopperSpice KitchenSink Documentation](https://www.copperspice.com/documentation-kitchensink.html)
* [CopperSpice Overview: KitchenSink Demo](https://www.copperspice.com/docs/cs_overview/demo-ks.html)
* [CopperSpice Overview: Build Configuration](https://www.copperspice.com/docs/cs_overview/start-config.html)
* [CopperSpice Overview: Quick Start](https://www.copperspice.com/docs/cs_overview/start-quick-app.html)

---

## Tasks

### 1. Prerequisites

Before building the KitchenSink application in this exercise, ensure that the following tools are installed on your system:

* **CMake** (version 3.18 or newer)
* **gnu make** build system
* **gcc** (version 12 or newer)

Check the installed versions by running:
```bash
cmake --version
make --version
g++ --version
```

If any of these fail, install them by:
```bash
sudo apt update
sudo apt install -y \
    cmake           \
    g++             \
    binutils        
```

If you know, what you are doing, you can also use **ninja-build**, **llvm** or even build everything in **msys**.

#### 1.1 Installing the Libraries Prerequisites

In order to build a project with CopperSpice, a couple of libraries need to be installed. 
Some of them can be installed by using apt, but the CopperSpice libries themself need to be downloaded manually.
Start by installing all the apt-available libraries for your system - if you are not running ubuntu, you can check the library prerequisits on the [official CopperSpice website](https://www.copperspice.com/docs/cs_overview/requirements-unix.html#build-extra).
You can check which distro you are running with the following command:
```bash
lsb_release -a
```

> ⚠️ **Security Disclaimer**
> 
> Never run scripts or commands from the internet with `sudo` unless you fully understand what they do. Executing untrusted code as root can compromise your system.

On ubuntu you may run:
```bash
sudo apt update
sudo apt install -y                 \
  libopengl-dev                     \               
  libfreetype6-dev                  \
  libfontconfig1-dev                \
  libglib2.0-dev                    \
  libgstreamer1.0-dev               \
  libgstreamer-plugins-base1.0-dev  \
  libice-dev                        \
  libaudio-dev                      \
  libgl1-mesa-dev                   \
  libc6-dev                         \
  libsm-dev                         \ 
  libxcursor-dev                    \
  libxext-dev                       \
  libxfixes-dev                     \
  libxi-dev                         \
  libxinerama-dev                   \
  libxrandr-dev                     \
  libxrender-dev                    \
  libxkbcommon-dev                  \
  libxkbcommon-x11-dev              \
  libx11-dev                        \
  libxcb1-dev                       \
  libx11-xcb-dev                    \
  libxcb-glx0-dev                   \
  libxcb-icccm4-dev                 \
  libxcb-image0-dev                 \
  libxcb-keysyms1-dev               \
  libxcb-render0-dev                \
  libxcb-render-util0-dev           \
  libxcb-randr0-dev                 \
  libxcb-shape0-dev                 \
  libxcb-shm0-dev                   \
  libxcb-sync-dev                   \
  libxcb-xfixes0-dev                \
  libxcb-xinerama0-dev              \
  libxcb-xkb-dev                    \
  libpulse-dev                      
```

<details>
<summary><strong>Required Development Libraries Explaination</strong></summary>

| Library Name                     | GitHub Link                                                                         | Description                                                                      |
| -------------------------------- | ----------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| libfreetype6-dev                 | [FreeType](https://github.com/freetype)                                             | A freely available software library to render fonts.                             |
| libfontconfig1-dev               | [Fontconfig](https://gitlab.freedesktop.org/fontconfig/fontconfig)                  | A library for configuring and customizing font access.                           |
| libglib2.0-dev                   | [GLib](https://github.com/GNOME/glib)                                               | Low-level core library that forms the basis for projects such as GTK+ and GNOME. |
| libgstreamer1.0-dev              | [GStreamer](https://gitlab.freedesktop.org/gstreamer/gstreamer)                     | A pipeline-based multimedia framework.                                           |
| libgstreamer-plugins-base1.0-dev | [GStreamer Plugins Base](https://gitlab.freedesktop.org/gstreamer/gst-plugins-base) | A set of well-supported plugins for GStreamer.                                   |
| libice-dev                       | [libICE](https://gitlab.freedesktop.org/xorg/lib/libICE)                            | Inter-Client Exchange library for X11.                                           |
| libaudio-dev                     | [libaudio](https://github.com/jamlib/libaudio)                                      | Library for manipulation of audio files.                                         |
| libgl1-mesa-dev                  | [Mesa 3D](https://gitlab.freedesktop.org/mesa/mesa)                                 | Open-source implementation of the OpenGL specification.                          |
| libc6-dev                        | [GNU C Library](https://sourceware.org/glibc/)                                      | Used as the C library in the GNU system.                                         |
| libsm-dev                        | [libSM](https://gitlab.freedesktop.org/xorg/lib/libSM)                              | Session Management library for X11.                                              |
| libxcursor-dev                   | [libXcursor](https://gitlab.freedesktop.org/xorg/lib/libXcursor)                    | X11 Cursor management library.                                                   |
| libxext-dev                      | [libXext](https://gitlab.freedesktop.org/xorg/lib/libXext)                          | Miscellaneous X11 protocol extensions.                                           |
| libxfixes-dev                    | [libXfixes](https://gitlab.freedesktop.org/xorg/lib/libXfixes)                      | Provides 'fixes' extension to the X11 protocol.                                  |
| libxi-dev                        | [libXi](https://gitlab.freedesktop.org/xorg/lib/libXi)                              | X Input extension library.                                                       |
| libxinerama-dev                  | [libXinerama](https://gitlab.freedesktop.org/xorg/lib/libXinerama)                  | API for Xinerama extension to X11.                                               |
| libxrandr-dev                    | [libXrandr](https://gitlab.freedesktop.org/xorg/lib/libXrandr)                      | X Resize, Rotate and Reflect extension library.                                  |
| libxrender-dev                   | [libXrender](https://gitlab.freedesktop.org/xorg/lib/libXrender)                    | X Rendering Extension client library.                                            |
| libxkbcommon-dev                 | [libxkbcommon](https://github.com/xkbcommon/libxkbcommon)                           | Keymap handling for toolkits and window systems.                                 |
| libxkbcommon-x11-dev             | [libxkbcommon-x11](https://github.com/xkbcommon/libxkbcommon)                       | X11 support for libxkbcommon.                                                    |
| libx11-dev                       | [libX11](https://gitlab.freedesktop.org/xorg/lib/libX11)                            | X11 client-side library.                                                         |
| libxcb1-dev                      | [libxcb](https://gitlab.freedesktop.org/xorg/lib/libxcb)                            | X protocol C-language Binding.                                                   |
| libx11-xcb-dev                   | [libX11-xcb](https://gitlab.freedesktop.org/xorg/lib/libX11)                        | Xlib/XCB interface library.                                                      |
| libxcb-glx0-dev                  | [libxcb-glx](https://gitlab.freedesktop.org/xorg/lib/libxcb)                        | XCB GLX Extension.                                                               |
| libxcb-icccm4-dev                | [libxcb-icccm](https://gitlab.freedesktop.org/xorg/lib/libxcb)                      | XCB ICCCM extension.                                                             |
| libxcb-image0-dev                | [libxcb-image](https://gitlab.freedesktop.org/xorg/lib/libxcb)                      | XCB image extension.                                                             |
| libxcb-keysyms1-dev              | [libxcb-keysyms](https://gitlab.freedesktop.org/xorg/lib/libxcb)                    | XCB keysyms extension.                                                           |
| libxcb-render0-dev               | [libxcb-render](https://gitlab.freedesktop.org/xorg/lib/libxcb)                     | XCB render extension.                                                            |
| libxcb-render-util0-dev          | [libxcb-render-util](https://gitlab.freedesktop.org/xorg/lib/libxcb)                | Convenience functions for XCB Render extension.                                  |
| libxcb-randr0-dev                | [libxcb-randr](https://gitlab.freedesktop.org/xorg/lib/libxcb)                      | XCB RandR extension.                                                             |
| libxcb-shape0-dev                | [libxcb-shape](https://gitlab.freedesktop.org/xorg/lib/libxcb)                      | XCB shape extension.                                                             |
| libxcb-shm0-dev                  | [libxcb-shm](https://gitlab.freedesktop.org/xorg/lib/libxcb)                        | XCB shared memory extension.                                                     |
| libxcb-sync-dev                  | [libxcb-sync](https://gitlab.freedesktop.org/xorg/lib/libxcb)                       | XCB sync extension.                                                              |
| libxcb-xfixes0-dev               | [libxcb-xfixes](https://gitlab.freedesktop.org/xorg/lib/libxcb)                     | XCB XFixes extension.                                                            |
| libxcb-xinerama0-dev             | [libxcb-xinerama](https://gitlab.freedesktop.org/xorg/lib/libxcb)                   | XCB Xinerama extension.                                                          |
| libxcb-xkb-dev                   | [libxcb-xkb](https://gitlab.freedesktop.org/xorg/lib/libxcb)                        | XCB XKB extension.                                                               |
| libpulse-dev                     | [PulseAudio](https://gitlab.freedesktop.org/pulseaudio/pulseaudio)                  | Sound server for POSIX OSes.                                                     |

</details>

---

#### 1.2 Installing the CS-Libraries
Download the CopperSpice prebuilt libraries from the [original website](https://download.copperspice.com/copperspice/binary/cs-1.9/).
Make sure, to collect the link for your specific platform. 

Since I recommend installing libraries for such experiments into a visible dir into your home directory. Let's create one first, e.g.:
```bash
mkdir -p ~/STEMgraph/cpplibs/cs1.9_libs/
```

Now download the binaries and store them in the lib directory. On ubuntu 24.04, it looks like this:
```bash
wget -O ~/STEMgraph/cpplibs/cs1.9.tar.bz2 https://download.copperspice.com/copperspice/binary/cs-2.0/copperspice-1.9.2-ubuntu24.04-x64.tar.bz2
```

Unpack the libraries into the prepared directory:
```bash
tar -xvjf ~/STEMgraph/cpplibs/cs1.9.tar.bz2 -C ~/STEMgraph/cpplibs/cs1.9_libs
```
* `xvjf`: tells tar to extract (`x`), be verbose (`v`), use _bzip2_ compression (`j`), and operate on the provided file (`f = ~/STEMgraph_libs/cs1.9.tar.bz2`).

### 2. Downloading KitchenSink Source Code

In order to build the KitchenSink project, we need to acquire its sources.
Clone the KitchenSink repository from the CopperSpice GitHub page:

Prepare a directory, e.g.:
```bash
mkdir -p ~/STEMgraph/experiments/copperspice
```

```bash
git clone https://github.com/copperspice/kitchensink.git ~/STEMgraph/experiments/copperspice/kitchensink
```

Set the `kitchensink` directory as your `pwd`:

```bash
cd ~/STEMgraph/experiments/copperspice/kitchensink
```

### 4. Configuring the Build Environment
For the build process, we will need the full path of the CS-cmake files, let's store this behind a shell value identifier first.
Of course, you could manually punch the path in, but let's use a helper function to get the exact location of where the libraries were installed by running:
```bash
CS_LIB_PREFIX=$(find ~ -type d -path "*/lib/cmake/CopperSpice")
```
I am memorizing the path into the identifier `CS_LIB_PREFIX`, but you could use another abbreviation if you like. 
You can check whether it was set correctly by printing it:
```bash
echo $CS_LIB_PREFIX
```

If set correctly we can now run the CMake configuration command:

```bash
cmake -B ./build -DCMAKE_PREFIX_PATH=$CS_LIB_PREFIX
```
While `-B ./build` generates a new subdirectory called build, the `-DCMAKE_PREFIX_PATH` parameter sets the location where **CMake** searches for your CopperSpice library

### 5. Building and Installing KitchenSink

Build the application using:

```bash
cmake --build ./build
```

This executable is not yet ready to run, since it requires some dynamically linked libraries in a specific relative path to itself. 
To create a path with all dependencies we run:
```bash
cmake --install ./build --prefix ./deploy
```

### 6. Running the Application

Navigate to the deployment directory:

```bash
./deploy/kitchensink
```

You should see the KitchenSink GUI application launch, showcasing various demos and features.
Click through the application and explore the possibilities of the library!

---

## Questions

1. **What is the purpose of the `CMAKE_PREFIX_PATH` variable in the CMake configuration?**
2. **Why is it recommended to use a separate build directory when configuring the build with CMake?**
3. **What is the role of the `cmake --build` and `cmake --install` commands in the build process?**

---

## Advice

Building the CopperSpice KitchenSink demo is an excellent way to gain hands-on experience with modern C++ development tools and practices. As you work through this exercise, pay close attention to the configuration options and directory structures. Understanding these aspects will be invaluable when you start developing your own applications using CopperSpice.

If you encounter any issues during the build process, refer to the CopperSpice documentation and forums for guidance. Remember, consistent practice and exploration are key to mastering software development skills. Keep experimenting with different configurations and explore the various demos within KitchenSink to deepen your understanding.

---
