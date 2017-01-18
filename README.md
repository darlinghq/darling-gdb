# GDB for Darling

This is GDB modified for use with Darling. It enables you to debug Mach-O code loaded by dyld (via Darling's mldr).

I build GDB like this, adapt it to your preferences:

```
./configure --disable-werror --disable-binutils --disable-etc \
	--disable-gas --disable-gold --disable-gprof --disable-ld \
	--enable-gdbserver=auto --enable-64-bit-bfd --disable-install-libbfd \
	--disable-install-libiberty --without-guile --disable-readline \
	--with-system-readline --without-zlib --with-system-zlib \
	--with-separate-debug-dir=/usr/lib/debug --without-expat \
	--without-lzma --enable-nls --enable-targets=all --program-suffix=-darling
```

## Usage example

TODO: This needs to be adapted for use in Darling's containers!

```
export DYLD_ROOT_PATH=/usr/local/libexec/darling
gdb-darling /usr/local/libexec/mldr
(gdb) r /some/apple/binary
```

Explanation: `mldr` is a simple Mach-O loader that loads Apple's dynamic loader (`dyld`) and hands control over to it.

You should be able to see valid backtraces where debug info is available:

```
(gdb) bt
#0  puts (s=0x7ffff7618faa "Hello world") at /home/lubos/Projects/darling/src/libc/stdio/FreeBSD/puts.c:64
#1  0x00007ffff7618f7d in main () from /home/lubos/Projects/hello
#2  0x00007ffef74e6455 in start () from /usr/local/libexec/darling/usr/lib/system/libdyld.dylib
#3  0x00007ffef74e6455 in start () from /usr/local/libexec/darling/usr/lib/system/libdyld.dylib
#4  0x0000000000000000 in ?? ()
```

Or examine what libraries are loaded at which addresses:

```
(gdb) info sharedlibrary 
From                To                  Syms Read   Shared Object Library
0x00007ffff7ddaaa0  0x00007ffff7df5de0  Yes         /lib64/ld-linux-x86-64.so.2
0x00007ffff7bc2a60  0x00007ffff7bcf871  Yes         /lib/x86_64-linux-gnu/libpthread.so.0
0x00007ffff79b9d80  0x00007ffff79ba93e  Yes         /lib/x86_64-linux-gnu/libdl.so.2
0x00007ffff763a910  0x00007ffff77642c3  Yes         /lib/x86_64-linux-gnu/libc.so.6
0x00007ffff7618f60  0x00007ffff7619018  Yes (*)     /home/lubos/Projects/hello
0x00007ffff7ff0780  0x00007ffff7ff1210  Yes         /usr/local/libexec/darling/usr/lib/libSystem.B.dylib
0x00007ffff7fec990  0x00007ffff7fed020  Yes (*)     /usr/local/libexec/darling/usr/lib/system/libsystem_sandbox.dylib
0x00007ffff7fe9900  0x00007ffff7fea070  Yes         /usr/local/libexec/darling/usr/lib/system/libquarantine.dylib
0x00007ffff7fe4930  0x00007ffff7fe7168  Yes (*)     /usr/local/libexec/darling/usr/lib/system/libremovefile.dylib
0x00007ffff7fd5a20  0x00007ffff7fe1350  Yes (*)     /usr/local/libexec/darling/usr/lib/system/libcopyfile.dylib
0x00007ffff7e1a550  0x00007ffff7e28b21  Yes         /usr/local/libexec/darling/usr/lib/system/libunwind.dylib
0x00007ffff7fd26b0  0x00007ffff7fd3478  Yes (*)     /usr/local/libexec/darling/usr/lib/system/libsystem_coreservices.dylib
0x00007ffef75d5f90  0x00007ffef7614070  Yes (*)     /usr/local/libexec/darling/usr/lib/libcommonCrypto.dylib
0x00007ffef7587450  0x00007ffef75bb6e8  Yes         /usr/local/libexec/darling/usr/lib/system/libsystem_info.dylib
0x00007ffff7e01200  0x00007ffff7e13400  Yes (*)     /usr/local/libexec/darling/usr/lib/system/libsystem_notify.dylib
0x00007ffef753cf80  0x00007ffef7568a68  Yes         /usr/local/libexec/darling/usr/lib/system/libdispatch.dylib
0x00007ffff7fcd2e0  0x00007ffff7fcf9f0  Yes (*)     /usr/local/libexec/darling/usr/lib/system/libsystem_blocks.dylib
0x00007ffef74f2750  0x00007ffef752c6ad  Yes         /usr/local/libexec/darling/usr/lib/system/libsystem_malloc.dylib
0x00007ffef74ee5e0  0x00007ffef74ef098  Yes (*)     /usr/local/libexec/darling/usr/lib/system/libkeymgr.dylib
0x00007ffef74e2620  0x00007ffef74e8554  Yes (*)     /usr/local/libexec/darling/usr/lib/system/libdyld.dylib
0x00007ffef74c6330  0x00007ffef74d65a0  Yes         /usr/local/libexec/darling/usr/lib/system/liblaunch.dylib
0x00007ffef72c3500  0x00007ffef743749c  Yes         /usr/local/libexec/darling/usr/lib/system/libsystem_c.dylib
0x00007ffef726fde0  0x00007ffef72aa294  Yes         /usr/local/libexec/darling/usr/lib/system/libsystem_m.dylib
0x00007ffef72638d0  0x00007ffef726c7c0  Yes (*)     /usr/local/libexec/darling/usr/lib/system/libmacho.dylib
0x00007ffef71d5dd0  0x00007ffef7229ce0  Yes         /usr/local/libexec/darling/usr/lib/system/libsystem_kernel.dylib
0x00007ffff7dfd790  0x00007ffff7dfe01c  Yes (*)     /usr/local/libexec/darling/usr/lib/system/libsystem_duct.dylib
0x00007ffef71bad40  0x00007ffef71cc1f8  Yes (*)     /usr/local/libexec/darling/usr/lib/system/libcompiler_rt.dylib
0x00007ffef7173970  0x00007ffef71b10d0  Yes (*)     /usr/local/libexec/darling/usr/lib/libresolv.9.dylib
(*): Shared library is missing debugging information.

```

