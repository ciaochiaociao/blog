---
title: Make/Gnumake/Cmake/Autoconf/Autotool/Automake
---

ref: http://www.idryman.org/blog/2016/03/10/autoconf-tutorial-1/
ref: https://en.wikipedia.org/wiki/GNU_Autotools

## Types of `Make`
 - Make `make` (which is `gmake` mostly)
 - GNU Make `gmake`
 - BSD Make `pmake`
 - CMake
 
## AutoTool
 - GNU Autoconf
 - GNU Automake
 - GNU Libtool
 - Gnulib

![](https://upload.wikimedia.org/wikipedia/commons/thumb/8/84/Autoconf-automake-process.svg/600px-Autoconf-automake-process.svg.png)

## Autoconf 
- Input: `configure.in` / `configure.ac`
- Output: `configure`

```
# Initialize AC: ACINIT( package_name, version, bug_report_address)
AC_INIT([my_program], [0.1])
# Initialize Automake
AM_INIT_AUTOMAKE
# AM stands for Automake commands, AC stands for Autoconf commands
# We use libraries
AC_PROG_RANLIB
# Let's check for a C++ compiler
AC_LANG(C++)
# Let's set a C++ compiler
AC_PROG_CXX
# This suppresses the error we get for also having the cc version of helper library.
AC_SUBST([CC])
# Let's specify where the Makefiles should be produced.
# These are the same locations as your Makefile.in's, but named as Makefile only
# We need to do this because both Autoconf and Automake read this file and
# produce Makefiles from this list.
AC_CONFIG_FILES([Makefile my_inc/Makefile src/Makefile Makefile.config])
# Finally produce "configure" script
AC_OUTPUT
```

```
autoconf
autoreconf -i  # -i stands for install/create if needed  # require README, INSTALL, NEWS, AUTHORS, ChangeLog, COPYING
```

## Automake
- Input: `Makefile.am` `configure.ac`/`.in` and other files
- Output: `Makefile.in`

	creating a command in CLI by `install` 
	```
	bin_PROGRAMS = hello
	hello_SOURCES = hello.c
	```

## `./configure`
 - creates a subscript `config.status`, 
 - input: `Makefile.in`
 - output: `Makefile` or `Makefile.config` that in `include`d in the `Makefile`)
 - auto-generated capabilities:
 ```
 make
 make all
 make install
 make install-strip  # no debug signal
 make uninstall
 DESTDIR="$t" make install

 make dist  # create tar ball file `.tar.gz`
 
 make maintainer-clean  # Erase files that generated by autoconf.
 make distclean  # Additionally erase anything ./configure created.
 make clean

 make check
 make installcheck
 make distcheck
 ```