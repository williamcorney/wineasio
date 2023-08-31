# WineASIO -macOS OSX friendly

WineASIO provides an ASIO to JACK driver for WINE.

ASIO is a low-latency driver commonly used in audio workstation programs.

You can, for example, use with Logic Pro / Studio One (together with [JACK](https://jackaudio.org/downloads/)

### INSTALLATION

Do the following to build for 64-bit Wine.  Note the path for **PREFIX** in the **Makefile.mk** has alread been amended to **/usr/local**

1.  Open a terminal at the folder where you downloaded and extracted wineasio,  type **make 64 -j16** and hit enter.
2.  Copy wineasio.dll.so and winesasio.dll files from the build64 subfolder into their respective
    LIB/WINE/X86_64-UNIX and LIB/WINE/X86_64-WINDOWS folders  (see below)
  
| Variant                           | Where to install wineasio.dll.so  and wineasio.dll           |
|-----------------------------------|--------------------------------------------------------------|
| Official Wine                     | /usr/local/lib/wine/x86_64-unixX86_64-unix                   |
|                                   | /usr/local/lib/wine/x86_64-unixX86_64-windows                |

3.  Open a terminal, type **wine64 cmd**, type regsvr32 wineasio.dll

#### CUSTOM WINEPREFIX

If using crossover or wineskin winery you should place wineasio.dll and wineasio.dll in the above paths AND also in the locations mentioned below!!


| Variant                           | X86_64-unix & X86_64-windows folder paths                    |
|-----------------------------------|--------------------------------------------------------------|
| Official Wine                     | /usr/local                                                   |
| Wineskin wrapper /Wineskin winery | /Applications/MyWrapper.app/Contents/SharedSupport/Wine      |
| Crossover                         | /Applications/Crossover.app/Contents/SharedSupport/Crossover |
|                                   |                                                              |
|                                   |                                                              |




3.  (CROSSOVER users only) - Open a command prompt,  type the following and hit enter

WINEPREFIX="/Users/username/Library/Application Support/CrossOver/Bottles/NAMEOFBOTTLE" wine64 cmd

4.  (WINESKINWINERY users only) - Open a command prompt,  type the following and hit enter

WINEPREFIX="/Applications/EP64.app/Contents/SharedSupport/prefix" wine64 cmd

5.  Type regsvr32 wineasio.dll and hit enter.  You can close the command prompt

6.  Open a command prompt through the Crossover GUI in the bottle you want to use and type the same command, regsvr32 wineasio.dll and hit enter.

7.  If all went well you should see a Succesfully registered message




To install on 32bit wine <= 6.5 (substitute with the path to the 32-bit wine libs for your distro).

```sh
sudo cp build32/wineasio.dll.so /usr/lib/i386-linux-gnu/wine/wineasio.dll.so
```

To install on 64bit wine <= 6.5 (substitute with the path to the 64-bit wine libs for your distro).

```sh
sudo cp build64/wineasio.dll.so /usr/lib/x86_64-linux-gnu/wine/wineasio.dll.so
```

Finally the dll must be registered in the wineprefix.
For both 32 and 64-bit wine do:

```sh
regsvr32 wineasio.dll

```

On a 64-bit system with wine supporting both 32 and 64-bit applications,
regsrv32 will register the 32-bit driver in a 64-bit prefix,
use the following command to register the 64-bit driver in a 64-bit wineprefix:

```sh
wine64 regsvr32 wineasio.dll
```

#### WINE > 6.5

To install on 32bit wine > 6.5 (substitute with the path to the 32-bit wine libs for your distro).

```sh
sudo cp build32/wineasio.dll /usr/lib/i386-linux-gnu/wine/i386-windows/wineasio.dll
sudo cp build32/wineasio.dll.so /usr/lib/i386-linux-gnu/wine/i386-unix/wineasio.dll.so
```

To install on 64bit wine > 6.5 (substitute with the path to the 64-bit wine libs for your distro).

```sh
sudo cp build64/wineasio.dll /usr/lib/x86_64-linux-gnu/wine/x86_64-windows/wineasio.dll
sudo cp build64/wineasio.dll.so /usr/lib/x86_64-linux-gnu/wine/x86_64-unix/wineasio.dll.so
```

Finally the dll must be registered in the wineprefix.
For both 32 and 64-bit wine do:  
(substitute with the path to the 32-bit wine libs for your distro)

```sh
regsvr32 /usr/lib/i386-linux-gnu/wine/i386-windows/wineasio.dll

```

On a 64-bit system with wine supporting both 32 and 64-bit applications,
regsrv32 will register the 32-bit driver in a 64-bit prefix,
use the following command to register the 64-bit driver in a 64-bit wineprefix:  
(substitute with the path to the 64-bit wine libs for your distro)

```sh
wine64 regsvr32 /usr/lib/x86_64-linux-gnu/wine/x86_64-windows/wineasio.dll
```

#### CUSTOM WINEPREFIX

regsvr32 registers the ASIO COM object in the default prefix `~/.wine`.  
To use another prefix specify it explicitly, like:

```sh
env WINEPREFIX=~/asioapp regsvr32 wineasio.dll
```

### GENERAL INFORMATION

ASIO apps get notified if the jack buffersize changes.

WineASIO can slave to the jack transport.

WineASIO can change jack's buffersize if so desired. Must be enabled in the registry, see below.

The configuration of WineASIO is done with Windows registry (`HKEY_CURRENT_USER\Software\Wine\WineASIO`).  
All these options can be overridden by environment variables.  
There is also a GUI for changing these settings, which WineASIO will try to launch when the ASIO "panel" is clicked.

The registry keys are automatically created with default values if they doesn't exist when the driver initializes.
The available options are:

#### [Number of inputs] & [Number of outputs]
These two settings control the number of jack ports that WineASIO will try to open.  
Defaults are 16 in and 16 out.  Environment variables are `WINEASIO_NUMBER_INPUTS` and `WINEASIO_NUMBER_OUTPUTS`.

#### [Autostart server]

Defaults to off (0), setting it to 1 enables WineASIO to launch the jack server.  
See the jack documentation for further details.  
The environment variable is `WINEASIO_AUTOSTART_SERVER`, and it can be set to on or off.

#### [Connect to hardware]
Defaults to on (1), makes WineASIO try to connect the ASIO channels to the physical I/O ports on your hardware.  
Setting it to 0 disables it.  
The environment variable is `WINEASIO_CONNECT_TO_HARDWARE`, and it can be set to on or off.

#### [Fixed buffersize]
Defaults to on (1) which means the buffer size is controlled by jack and WineASIO has no say in the matter.  
When set to 0, an ASIO app will be able to change the jack buffer size when calling CreateBuffers().  
The environment variable is `WINEASIO_FIXED_BUFFERSIZE` and it can be set to on or off.

#### [Preferred buffersize]
Defaults to 1024, and is one of the sizes returned by `GetBufferSize()`, see the ASIO documentation for details.  
Must be a power of 2.

The other values returned by the driver are hardcoded in the source,  
see `ASIO_MINIMUM_BUFFERSIZE` which is set at 16, and `ASIO_MAXIMUM_BUFFERSIZE` which is set to 8192.  
The environment variable is `WINEASIO_PREFERRED_BUFFERSIZE`.

Be careful, if you set a size that isn't supported by the backend, the jack server will most likely shut down,
might be a good idea to change `ASIO_MINIMUM_BUFFERSIZE` and `ASIO_MAXIMUM_BUFFERSIZE` to values you know work on your system before building.

In addition there is a `WINEASIO_CLIENT_NAME` environment variable,
that overrides the JACK client name derived from the program name.

### CHANGE LOG

#### 1.1.0
* 18-FEB-2022: Various bug fixes (falkTX)
* 24-NOV-2021: Fix compatibility with Wine > 6.5

#### 1.0.0
* 14-JUL-2020: Add packaging script
* 12-MAR-2020: Fix control panel startup
* 08-FEB-2020: Fix code to work with latest Wine
* 08-FEB-2020: Add custom GUI for WineASIO settings, made in PyQt5 (taken from Cadence project code)

#### 0.9.2
* 28-OCT-2013: Add 64-bit support and some small fixes

#### 0.9.1
* 15-OCT-2013: Various bug fixes (JH)

#### 0.9.0
* 19-FEB-2011: Nearly complete refactoring of the WineASIO codebase (asio.c) (JH)

#### 0.8.1
* 05-OCT-2010: Code from Win32 callback thread moved to JACK process callback, except for bufferSwitch() call.
* 05-OCT-2010: Switch from int to float for samples.

#### 0.8.0
* 08-AUG-2010: Forward port JackWASIO changes... needs testing hard. (PLJ)

#### 0.7.6
* 27-DEC-2009: Fixes for compilation on 64-bit platform. (PLJ)

#### 0.7.5
* 29-Oct-2009: Added fork with call to qjackctl from ASIOControlPanel(). (JH)
* 29-Oct-2009: Changed the SCHED_FIFO priority of the win32 callback thread. (JH)
* 28-Oct-2009: Fixed wrongly reported output latency. (JH)

#### 0.7.4
* 08-APR-2008: Updates to the README.TXT (PLJ)
* 02-APR-2008: Move update to "toggle" to hopefully better place (PLJ)
* 24-MCH-2008: Don't trace in win32_callback.  Set patch-level to 4. (PLJ)
* 09-JAN-2008: Nedko Arnaudov supplied a fix for Nuendo under WINE.

#### 0.7.3
* 27-DEC-2007: Make slaving to jack transport work, correct port allocation bug. (RB)

#### 0.7
* 01-DEC-2007: In a fit of insanity, I merged JackLab and Robert Reif code bases. (PLJ)

#### 0.6
* 21-NOV-2007: add dynamic client naming (PLJ)

#### 0.0.3
* 17-NOV-2007: Unique port name code (RR)

#### 0.5
* 03-SEP-2007: port mapping and config file (PLJ)

#### 0.3
* 30-APR-2007: corrected connection of in/outputs (RB)

#### 0.1
* ???????????: Initial RB release (RB)

#### 0.0.2
* 12-SEP-2006: Fix thread bug, tidy up code (RR)

#### 0.0.1
* 31-AUG-2006: Initial version (RR)

### LEGAL STUFF

Copyright (C) 2006 Robert Reif  
Portions copyright (C) 2007 Ralf Beck  
Portions copyright (C) 2007 Johnny Petrantoni  
Portions copyright (C) 2007 Stephane Letz  
Portions copyright (C) 2008 William Steidtmann  
Portions copyright (C) 2010 Peter L Jones  
Portions copyright (C) 2010 Torben Hohn  
Portions copyright (C) 2010 Nedko Arnaudov  
Portions copyright (C) 2011 Christian Schoenebeck  
Portions copyright (C) 2013 Joakim Hernberg  
Portions copyright (C) 2020 Filipe Coelho  

The WineASIO library code is licensed under LGPL v2.1, see COPYING.LIB for more details.  
The WineASIO settings UI code is licensed under GPL v2+, see COPYING.GUI for more details.  
