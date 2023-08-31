# WineASIO -macOS OSX friendly Guide

WineASIO provides an ASIO to JACK driver for WINE.

ASIO is a low-latency driver commonly used in audio workstation programs.

Wineasio enables low latency audio between your Windows VST running in WINE and your OSX system

You must install [JACK](https://jackaudio.org/downloads/) on your OSX system and set an appropriate soundcard as the output device.  
You can for convenience configure multiple profiles in Jack with different output devices.

### PREPARATION

Before you can build and install wineasio.dll and wineasio.dll.so you need to prepare a build environment by installing homebrew and compiling wine from source as detailed below.

Run these commands in terminal to install homebrew and some necessary additional packages:-

**/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"**
**brew install --formula bison flex mingw-w64 pkgconfig freetype**
**export PATH="$(brew --prefix bison)/bin:$PATH"
**

Optional - to install wineskin winery

**brew install --cask --no-quarantine gcenx/wine/wineskin
**

1. Dowload and extract https://dl.winehq.org/wine/source/8.x/wine-8.13.tar.xz
2. Open a terminal at the location you extracted wine-8.13.tar.xz
3. Run in terminal ./configure CC="clang" CXX="clang++" --enable-win64
4. Check that you see the line - configure: Finished. Do 'make' to compile.
5. Run in terminal make -j16 ( replace this number with your processor thread count).
8. Close all terminal windows once this process has completed - it took me about 10 mins as a rough idea.
9. Open a terminal window and check you can run wine64 and winebuild globally ( i.e you dont need to be in the wine source folder). If you cant you need to address this before moving on.
10. Run in terminal make install -j16 ( this also takes a couple of minutes)

### INSTALLATION

Do the following to build and register WINEASIPO for 64-bit Wine.  
Note the path for **PREFIX** in the **Makefile.mk** has already been amended to **/usr/local**

1.  Download and extract wineasio tarball,  open a terminal at this location,  type **make 64 -j16** and hit enter.
2.  Copy wineasio.dll.so and winesasio.dll files from the build64 subfolder into their respective X86_64-UNIX and X86_64-WINDOWS folders  (see below)
  
| File                              | Where to put a COPY of wineasio.dll.so and wineasio.dll      |
|-----------------------------------|--------------------------------------------------------------|
| wineasio.dll.so                   | /usr/local/lib/wine/x86_64-unixX86_64-unix                   |
| wineasio.dll                      | /usr/local/lib/wine/x86_64-unixX86_64-windows                |

3.  Open a terminal, type **wine64 cmd**, type regsvr32 wineasio.dll

#### CUSTOM WINEPREFIX

4.  If using crossover or wineskin winery you should additionly place wineasio.dll and wineasio.dll in these paths and complete the steps listed below


**WINESKIN WINERY
**
|-----------------------------------|--------------------------------------------------------------|
| wineasio.dll.so                   | /Applications/MyWrapper.app/Contents/SharedSupport/Wine/lib/wine/x86_64-unix    |
| wineasio.dll                      | /Applications/MyWrapper.app/Contents/SharedSupport/Wine/lib/wine/x86_64-windows    |

open a terminal, type **WINEPREFIX="/Applications/EP64.app/Contents/SharedSupport/prefix" wine64 cmd** and hit enter
type regsvr32 wineasio.dll and hit enter

**CROSSOVER
**
| wineasio.dll.so                   | /Applications/Crossover.app/Contents/SharedSupport/Crossover/lib/wine/x86_64-unix |
| wineasio.dll                      | /Applications/Crossover.app/Contents/SharedSupport/Crossover/lib/wine/x86_64-windows |

open a terminal, type **WINEPREFIX="/Users/username/Library/Application Support/CrossOver/Bottles/NAMEOFBOTTLE" wine64 cmd** and hit enter
type regsvr32 wineasio.dll and hit enter

5.  If all went well you should see a Succesfully registered message


