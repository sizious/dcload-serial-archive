# dcload-serial 1.0.6

**dcload** is a **Sega Dreamcast** (DC) serial loader written originally by 
[Andrew Kieschnick](http://napalm-x.thegypsy.com/andrewk/dc/), a.k.a.
**ADK/Napalm**. This program is part of 
[KallistiOS](http://gamedev.allusion.net/softprj/kos/), a.k.a. KOS.

**dcload** is a set of programs made to send and receive data from your Sega
Dreamcast system. The classic use of this tool is to send programs to the
Dreamcast in order to run and debug them. To be used, you must have a way to
connect your Dreamcast console to your computer, it can be one of the following:

* A **Coders Cable** (a serial cable, the historical way to do that). It can be
  a cable with the classical `RS-232`/`DE-9` connector or with the FT232RL USB-Serial.
* A **Broadband Adapter**, ref. `HIT-400`, often shortened as **BBA**, a 
  `10/100Mbits` network Ethernet card.
* A **LAN Adapter**, ref. `HIT-300`, a `10Mbits` network Ethernet card.

If you have a Coders Cable, you have to use `dcload-serial`. For the Broadband
Adapter or LAN Adapter, you have to use `dcload-ip`.

**dcload** is split in two components:

* `dcload`, the server part, meant to be run on the Dreamcast;
* `dc-tool`, the client part, executed from your computer.

## Features

* Load `elf`, `srec` and `bin` (binary transfers are compressed)
* PC I/O (read, write from/to the PC)
* Exception handler
* Debug Dreamcast programs remotely by using the **GDB-over-dcload** feature

## Building

1. You should have a working 
   [KallistiOS](http://gamedev.allusion.net/softprj/kos/) environment and of
   course the `sh-elf` toolchain installed (if you have installed KOS, you 
   already have everything ready).
2. Edit the `Makefile.cfg` file for your system and then run `make`.

## Installation

1. PC - run `make install`: installs `dc-tool`.
2. DC - `dcload`. You have two options.
   
* Directly burn to disc by using `cdrecord`:

  a. Navigate to `make-cd`
  b. Edit the `Makefile`
  c. Insert blank CD-R
  d. Run `make`. If `1ST_READ.BIN` hasn't been built yet, this `Makefile` will 
     build it.
 
* Create a CDI image to burn later (requires `mkisofs` and `cdi4dc` tools):

  a. `make -C ./host-src/misc` (build the lzo binary)
  b. `make -C ./target-src` (build the 1ST_READ.BIN)
  c. `mkisofs -C 0,11702 -V dcload-serial -G ./make-cd/IP.BIN -joliet -rock -l -o temp.iso ./target-src/1st_read/1st_read.bin`
  d. `cdi4dc temp.iso dcload-serial.cdi`

## Testing

Everything is located in the `example-src` directory.

* `dc-tool-ser -x console-test`: tests some PC I/O
* `dc-tool-ser -x exception-test`: generates an exception

## KOS GDB-over-dcload

To run a GNU debugger session over the dcload connection:

1. Build/obtain an `sh-elf` targetted GNU debugger
2. Put a `gdb_init()` call somewhere in the startup area of your
   KOS-based program (e.g. it's a good idea to put this call in your `main()`)
3. Build your program with the `-g` GCC switch to include debugging info
4. Launch your program using `dc-tool-ser -g -x <prog.elf>`
5. Launch `sh-elf-gdb` and connect to the `dc-tool` using `target remote :2159`
6. Squash bugs

## Notes

* **dcload**, both IP and Serial, are maintained by the KOS
  team. Please join the [KallistiOS list](http://sf.net/projects/cadcdev/) 
  for help with these tools.
* Tested systems: Debian GNU/Linux 2.2; Gentoo/Linux 2.6.7; Cygwin;
  Mac OSX 10.3.5 (Panther); macOS 10.15.2 (Catalina), MinGW/MSYS, 
  [DreamSDK](https://www.dreamsdk.org)
* `1.56M` and `500K` baud now supported with the FTDI USB-Serial driver, 
  including the driver built into macOS 10.12 and above.
  **Note:** Works with the cheap and commonly available FT232RL USB-Serial
  boards as well as the (outdated) FT232BM USB-Serial chip running at `6.144Mhz`.
  e.g.:
    - Linux:   `dc-tool-ser -t /dev/usb/tts/0 -b 1500000 -x <sh-executable>`
    - Windows: `dc-tool-ser -t COM4 -b 500000 -x <sh-executable>`
    - macOS:   `dc-tool-ser -t /dev/cu.usbserial-A50285BI -b 1500000 -x <sh-executable>`
* As of `1.0.4`, little-endian byte order is enforced in the host so dc-tool
  now runs on big-endian systems like a Mac.
* As of 1.0.3, serial speed is changed at runtime rather than compile time. 
* `115200` works fine in most cases but `57600` baud is the standard baud.
  There is an `-e` option that will enable an alternate `115200` which may work
  better in some rare cases. Use this only if the regular `115200` is unstable.
* Patches and improvements are welcome.

## Modern MacOS Notes

* This was tested on **Catalina 10.15.2** only, however it should work on pretty
  much any version of macOS. 
* Of course some sort of USB serial adapter must be used. The standard 
  **FT232RL USB-Serial** boards from China that are sold pretty much everywhere
  work great and are super cheap.
* Modern macOS supports the same speeds as the other platforms, currently 
  up to `1.56M` baud (`-b 1500000`). This was tested using Catalina and an 
  **FT232RL**.
* Compilation on macOS requires `libelf`, which can be easily installed using
  the [Homebrew package manager](https://brew.sh): `brew install libelf`
* The static compilation option cannot be used on macOS due to the way GCC
  works on macOS. However, when building the standard dynamically linked build,
  only libSystem is linked (confirmed with `otool -L`) which is available on
  all macOS systems, so the binary should still be just as portable.

## Credits

* [miniLZO](http://www.oberhumer.com/opensource/lzo/) was written by 
  [Markus Oberhumer](http://www.oberhumer.com/)
* There are some various files from `newlib-1.8.2` here and `video.s` was
  written by [Marcus Comstedt](https://mc.pp.se/dc/).
* win32 porting and implementation of `-t` by **Florian 'Proff' Schulze**
* bugfix and implementation of `-b` by **The Gypsy**
* fixes for cygwin by **Florian 'Proff' Schulze**
* Minor initialization fix in dcload for `gcc-3.4.x` and Serial protocol endian
  fixes by [Paul Boese a.k.a. Axlen](http://archives.dcemulation.org/www.axlen.com/www.geocities.com/pboese_sbcglobal.net/index.html)
* Fixes for Mac OSX (and testing) by **Dan Potter**
* Fixes for `libbfd` segfaults by **Atani**
* Tons of improvements and fixes by [SiZiOUS](https://sizious.com)
* Modern macOS testing by **Ben Baron a.k.a. einsteinx2**
