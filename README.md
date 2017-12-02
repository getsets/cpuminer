CPUMiner
==============

This is a multi-threaded CPU miner for Litecoin and Bitcoin,
fork of Jeff Garzik's reference cpuminer.

License: GPLv2.  See COPYING for details.

* Downloads:  https://sourceforge.net/projects/cpuminer/files/
* Git tree:   https://github.com/pooler/cpuminer

#### Table of contents

* [Dependencies](#dependencies)
* [Build](#build)
* [Usage instructions](#usage-instructions)

Dependencies:
============
* libcurl http://curl.haxx.se/libcurl/
* jansson http://www.digip.org/jansson/ (jansson is included in-tree)

Build
=====
##### Basic *nix build instructions:
```
./autogen.sh  # only needed if building from git repo
./nomacro.pl  # only needed if building on Mac OS X or with Clang
./configure CFLAGS="-O3 -march=native -funroll-loops -fomit-frame-pointer"
make
```
* If you want instant gratification for mac, run this in the cpuminer folder and then rerun autogen.sh.
```
curl https://source.jasig.org/cas-clients/mod_auth_cas/tags/mod_auth_cas-1.0.9.1/libcurl.m4 -o acinclude.m4i
```

#### Notes for AIX users:
 * To build a 64-bit binary, export OBJECT_MODE=64
 * GNU-style long options are not supported, but are accessible via configuration file

#### Cross Compile for Windows 64it, using Ubuntu
```
sudo apt-get install build-essential automake mingw-w64-x86-64-dev
git clone https://github.com/BitzenyCoreDevelopers/cpuminer.git
cd cpuminer/deps
./build_win_deps.sh
cd ..
autoreconf -fi -I./deps/x86_64-w64-mingw32/share/aclocal
./configure --host=x86_64-w64-mingw32 \
CFLAGS="-O3 -I./deps/x86_64-w64-mingw32/include -std=c99 -DWIN32 -DCURL_STATICLIB -DPTW32_STATIC_LIB" \
--with-libcurl=deps/x86_64-w64-mingw32 LDFLAGS="-L./deps/x86_64-w64-mingw32/lib -static"
make
```
* TRY: -Ofast -flto -march=native


#### Basic Windows build instructions, using MinGW:
* Install MinGW and the MSYS Developer Tool Kit (http://www.mingw.org/)
  * Make sure you have mstcpip.h in MinGW\include
* If using MinGW-w64, install pthreads-w64
* Install libcurl devel (http://curl.haxx.se/download.html)
  * Make sure you have libcurl.m4 in MinGW\share\aclocal
  * Make sure you have curl-config in MinGW\bin
* In the MSYS shell, run:
```
./autogen.sh  # only needed if building from git repo
LIBCURL="-lcurldll" ./configure CFLAGS="-O3"
make
```

#### Architecture-specific notes:
* ARM:
  * No runtime CPU detection. The miner can take advantage of some instructions specific to ARMv5E and later processors, but the decision whether to use them is made at compile time, based on compiler-defined macros.
  * To use NEON instructions, add "-mfpu=neon" to CFLAGS.
* x86:
  * The miner checks for SSE2 instructions support at runtime, and uses them if they are available.
* x86-64:
  * The miner can take advantage of AVX, AVX2 and XOP instructions, but only if both the CPU and the operating system support them.
    * Linux supports AVX starting from kernel version 2.6.30.
    * FreeBSD supports AVX starting with 9.1-RELEASE.
    * Mac OS X added AVX support in the 10.6.8 update.
    * Windows supports AVX starting from Windows 7 SP1 and Windows Server 2008 R2 SP1.
  * The configure script outputs a warning if the assembler doesn't support some instruction sets. In that case, the miner can still be built, but unavailable optimizations are left off.

Usage instructions
==================
Run "minerd --help" to see options.

#### Connecting through a proxy:  Use the --proxy option.
To use a SOCKS proxy, add a socks4:// or socks5:// prefix to the proxy host.

Protocols socks4a and socks5h, allowing remote name resolving, are also available since libcurl 7.18.0.
If no protocol is specified, the proxy is assumed to be a HTTP proxy.

When the --proxy option is not used, the program honors the http_proxyand all_proxy environment variables.

Also many issues and FAQs are covered in the forum thread
dedicated to this program
  https://bitcointalk.org/index.php?topic=55038.0
