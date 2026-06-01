AC_INIT([aalib],[1.4.0])
AC_CONFIG_SRCDIR([src/aalib.c])
AC_CANONICAL_HOST
AC_CANONICAL_TARGET
AM_INIT_AUTOMAKE([foreign])
AC_CONFIG_MACRO_DIRS([m4])

AC_CONFIG_HEADERS([src/config.h])

dnl Initialize the compiler and linker flags for AALIB applications

AALIB_CFLAGS=""
AALIB_LIBS="-laa"

dnl Output the video drivers we use
if test x$enable_video = xtrue; then
    if test "$VIDEO_SUBDIRS" = ""; then
        AC_MSG_ERROR(*** No video drivers are enabled!)
    fi
fi

dnl Checks for programs.

x_driver_test=yes
curses_driver_test=yes
linux_driver_test=yes
slang_driver_test=yes
force_curses_driver=false
x_kbddriver_test=yes
curses_kbddriver_test=yes
slang_kbddriver_test=yes
curses_mousedriver_test=yes
gpm_mousedriver_test=yes
x_mousedriver_test=yes

use_cc_g_flag=no
AC_PROG_CC
LT_INIT
AC_PROG_MAKE_SET
AC_PROG_INSTALL

dnl detect OS

AC_CHECK_PROG(system,uname,`uname`,unknown)
if test "x$system" = xLinux; then
AC_CHECK_PROG(machine,uname,`uname --machine`,unknown)
fi
AC_CHECK_INCLUDES_DEFAULT
AC_PROG_EGREP

AC_CHECK_LIB(m, pow)

if test "x$ac_cv_lib_m_pow" = xyes; then 
  AALIB_LIBS="$AALIB_LIBS -lm"
fi

cc_uses_g=yes
if test x$GCC = xyes; then
    if test x$ac_cv_prog_gcc_g = xyes; then
	:
    else
	cc_uses_g=no
    fi
fi
dnl command line options

AC_ARG_WITH(x11-driver,
        [  --with-x11-driver=no    Used to disable x11 driver],[
	if test x$withval = xno; then
	  x_driver_test=no
	fi
])
AC_ARG_WITH(slang-driver,
        [  --with-slang-driver=no   Used to disable slang driver],[
	if test x$withval = xno; then
	  slang_driver_test=no
	fi
])
AC_ARG_WITH(curses-driver,
        [  --with-curses-driver=no/yes Used to disable/force curses driver],[
	if test x$withval = xno; then
	  curses_driver_test=no
	fi
	if test x$withval = xyes; then
	  force_curses_driver=true
	fi
])
AC_ARG_WITH(gpm-mouse, 
	[  --with-gpm-mouse=base-dir  Specifies the base gpm directory],
	if test x$withval = xyes
	then 
		AC_MSG_WARN(Usage is: --with-gpm-mouse=base-dir)
	else
    LIBS="$LIBS -L$withval/lib -lgpm"
                AC_DEFINE([GPM_MOUSEDRIVER],[1],[Define to 1 to enable the GPM mouse driver.])
	        gpm_mousedriver=true
		CFLAGS="$CFLAGS -I$withval/include"
	        AALIB_LIBS="$AALIB_LIBS -L$withval/lib -lgpm"
		AC_MSG_RESULT(defining use_gpm)
	fi
)

if test  "$x_driver_test" = yes; then
AC_PATH_X
AC_PATH_XTRA
if test "x$no_x" = x; then
    x_driver=true
  AC_DEFINE([X11_DRIVER],[1],[Define to 1 to enable the X11 display driver.])
    LIBS="$LIBS $X_EXTRA_LIBS $X_LIBS -lX11"
    AALIB_LIBS="$AALIB_LIBS $X_EXTRA_LIBS $X_LIBS -lX11"
    CFLAGS="$CFLAGS $X_CFLAGS"
fi

fi
if test  "$gpm_mousedriver_test" = yes; then
  AC_CHECK_LIB(gpm, Gpm_Repeat,[
          AC_DEFINE([GPM_MOUSEDRIVER],[1],[Define to 1 to enable the GPM mouse driver.])
	  gpm_mousedriver=true
	  AALIB_LIBS="$AALIB_LIBS -lgpm"
          LIBS="$LIBS -lgpm"])
fi

dnl
dnl This part supplies reasonable defaults for CFLAGS, if they weren't
dnl specified by ''CFLAGS=flags ./configure''
dnl




search_ncurses=true
AC_DEFUN([AC_USE_SUNOS_CURSES], [
	search_ncurses=false
	curses_driver=true
	screen_manager="SunOS 4.x /usr/5include curses"
	AC_MSG_RESULT(Using SunOS 4.x /usr/5include curses)
  AC_DEFINE([SUNOS_CURSES],[1],[Define to 1 when building with SunOS curses.])
  AC_DEFINE([NO_COLOR_SUPPORT],[1],[Define to 1 when the selected curses lacks color support.])
  AC_DEFINE([USE_SYSV_CURSES],[1],[Define to 1 when using SysV curses APIs.])
	CFLAGS="$CFLAGS -I/usr/5include"
	XCURSES="xcurses.o /usr/5lib/libcurses.a /usr/5lib/libtermcap.a"
	AC_MSG_RESULT(Please note that some screen refreshs may fail)
	AC_MSG_WARN(Reconsider using Slang)
])

AC_DEFUN([AC_USE_OSF1_CURSES], [
       AC_MSG_RESULT(Using OSF1 curses)
       search_ncurses=false
       curses_driver=true
       screen_manager="OSF1 curses"
      AC_DEFINE([NO_COLOR_SUPPORT],[1],[Define to 1 when the selected curses lacks color support.])
      AC_DEFINE([USE_SYSV_CURSES],[1],[Define to 1 when using SysV curses APIs.])
       XCURSES="xcurses.o"
       LIBS="$LIBS -lcurses"
       AALIB_LIBS="$AALIB_LIBS -lcurses"
])

AC_DEFUN([AC_USE_SYSV_CURSES], [
	AC_MSG_RESULT(Using SysV curses)
        curses_driver=true
  AC_DEFINE([USE_SYSV_CURSES],[1],[Define to 1 when using SysV curses APIs.])
	XCURSES=""
	search_ncurses=false
	screen_manager="SysV/curses"
	LIBS="$LIBS -lcurses"
        AALIB_LIBS="$AALIB_LIBS -lcurses"
])

XCURSES=""



AC_ARG_WITH(sunos-curses,
	[  --with-sunos-curses     Used to force SunOS 4.x curses],[
	if test x$withval = xyes; then
		AC_USE_SUNOS_CURSES
	fi
])

AC_ARG_WITH(osf1-curses,
	[  --with-osf1-curses	Used to force OSF/1 curses],[
	if test x$withval = xyes; then
		AC_USE_OSF1_CURSES
	fi
])

AC_ARG_WITH(vcurses,
	[  --with-vcurses[=incdir] Used to force SysV curses],
	if test x$withval = xyes; then
		CFLAGS="$CFLAGS"
        else
		CFLAGS="$CFLAGS -I$withval"
	fi
	AC_USE_SYSV_CURSES
)
        	
AC_ARG_WITH(ncurses,
	[  --with-ncurses[=base-dir] Compile with ncurses/locate base dir],
	if test x$withval = xyes
	then
		search_ncurses=true
	else
		LIBS="$LIBS -L$withval/lib -lncurses"
		AALIB_LIBS="$AALIB_LIBS -L$withval/lib -lncurses"
		CFLAGS="$CFLAGS -I$withval/include"
		search_ncurses=false
                curses_driver=true
		screen_manager="ncurses"
    AC_DEFINE([USE_NCURSES],[1],[Define to 1 when using ncurses.])
	fi
)

if test  "$slang_driver_test" = yes; then
  AC_DEFUN([AC_SLANG], [
    if $search_slang
    then
        if test -f $1/$2
	then
	    LIBS1="$LIBS"
 	    LIBS="$LIBS $3"
            AC_CHECK_LIB(slang, SLsmg_init_smg)
            if test "x$ac_cv_lib_slang_SLsmg_init_smg" = xyes; then 
	      AC_MSG_RESULT(Found slang on $1/$2)
	      AALIB_LIBS="$AALIB_LIBS $3 -lslang"
 	      LIBS="$LIBS $3"
	      CFLAGS="$CFLAGS $4"
              slang_driver=true
	      search_slang=false
        AC_DEFINE([SLANG_DRIVER],[1],[Define to 1 to enable the S-Lang display driver.])
	    else
             unset ac_cv_lib_slang_SLsmg_init_smg
	     LIBS="$LIBS1"
	    fi
	fi
     fi
  ])
  AC_SLANG(/usr/include, slang.h, , -I/usr/include , "slang on /usr/include")
  AC_SLANG(/usr/include/slang, slang.h, , -I/usr/include/slang , "slang on /usr/include/slang")
  AC_SLANG(/usr/local/include, slang.h, , -I/usr/local/include/slang , "slang on /usr/local/include")
  AC_SLANG(/usr/local/include/slang, slang.h, , -I/usr/local/include/slang , "slang on /usr/local/include/slang")
  if test "$slang_driver" = true; then
    if test "$force_curses_driver" = false; then
      echo "Disabling curses driver since slang is available"
      curses_driver_test=no
    fi
  fi
fi

dnl
dnl Parameters: directory filename LIBS_append CFLAGS_append nicename
dnl
AC_DEFUN([AC_NCURSES], [
    if $search_ncurses
    then
        if test -f $1/$2
	then
	    LIBS1="$LIBS"
 	    LIBS="$LIBS $3"
            AC_CHECK_LIB(ncurses, initscr)
            if test "x$ac_cv_lib_ncurses_initscr" = xyes; then 
	      AC_MSG_RESULT(Found ncurses on $1/$2)
	      AALIB_LIBS="$AALIB_LIBS $3 -lncurses"
 	      LIBS="$LIBS $3"
	      CFLAGS="$CFLAGS $4"
	      search_ncurses=false
              curses_driver=true
	      screen_manager=$5
        AC_DEFINE([USE_NCURSES],[1],[Define to 1 when using ncurses.])
	    else
             unset ac_cv_lib_ncurses_initscr
	     LIBS="$LIBS1"
	    fi
	fi
    fi
])

dnl
dnl Parameters: directory filename LIBS_append CFLAGS_append nicename
dnl
AC_DEFUN([AC_CURSES], [
    if $search_ncurses
    then
        if test -f $1/$2
	then
	    LIBS1="$LIBS"
 	    LIBS="$LIBS $3"
            AC_CHECK_LIB(curses, initscr)
            if test "x$ac_cv_lib_curses_initscr" = xyes; then 
	      AC_MSG_RESULT(Found generic curses on $1/$2)
	      CFLAGS="$CFLAGS $4"
	      search_ncurses=false
              curses_driver=true
	      AALIB_LIBS="$AALIB_LIBS $3 -lcurses"
        AC_DEFINE([CURSES_DRIVER],[1],[Define to 1 to enable the curses display driver.])
	      screen_manager=$5
        AC_DEFINE([USE_CURSES],[1],[Define to 1 when using generic curses APIs.])
	    else
             unset ac_cv_lib_curses_initscr
	     LIBS="$LIBS1"
	    fi
	fi
    fi
])

if test  "$curses_driver_test" = yes; then
if $search_ncurses
then
  AC_MSG_CHECKING([location of curses])

    AC_NCURSES(/usr/include, ncurses.h, , -I/usr/include/ncurses , "ncurses on /usr/include")
    AC_NCURSES(/usr/include/ncurses, ncurses.h, , -I/usr/include/ncurses, "ncurses on /usr/include/ncurses")
    AC_NCURSES(/usr/local/include, ncurses.h, ,-L/usr/local/lib, -I/usr/local/include, "ncurses on /usr/local")
    AC_NCURSES(/usr/local/include/ncurses, ncurses.h, -L/usr/local/lib -L/usr/local/lib/ncurses, -I/usr/local/include/ncurses, "ncurses on /usr/local/include/ncurses")

    AC_NCURSES(/usr/local/include/ncurses, curses.h, -L/usr/local/lib, -I/usr/local/include/ncurses -DRENAMED_NCURSES, "renamed ncurses on /usr/local/.../ncurses")

    AC_NCURSES(/usr/include/ncurses, curses.h, , -I/usr/include/ncurses -DRENAMED_NCURSES, "renamed ncurses on /usr/include/ncurses")
    AC_CURSES(/usr/include, curses.h, , , "curses on /usr/include")

    dnl
    dnl We couldn't find ncurses, try SysV curses
    dnl
    if $search_ncurses 
    then
        AC_EGREP_HEADER(init_color, /usr/include/curses.h,
	    AC_USE_SYSV_CURSES)
    fi

    dnl
    dnl Try SunOS 4.x /usr/5{lib,include} ncurses
    dnl The flags SUNOS_CURSES, USE_BSD_CURSES and BUGGY_CURSES
    dnl should be replaced by a more fine grained selection routine
    dnl
    if $search_ncurses
    then
	if test -f /usr/5include/curses.h
	then
	    AC_USE_SUNOS_CURSES
        fi
    fi
fi
AC_CHECK_FUNCS(mousemask termattrs wredrawln redrawwin curs_set)
fi


dnl Checks for header files.
AC_CHECK_HEADERS(fcntl.h limits.h malloc.h sys/ioctl.h sys/time.h unistd.h)

dnl Checks for typedefs, structures, and compiler characteristics.
AC_CHECK_MEMBERS([struct stat.st_rdev],[AC_DEFINE(HAVE_ST_RDEV, 1,
			    [Define to 1 if your 'struct stat' has 'st_rdev'.
			     Deprecated, use 'HAVE_STRUCT_STAT_ST_RDEV'
			     instead.])])

AC_CHECK_HEADERS_ONCE([sys/time.h])
# Obsolete code to be removed.
if test $ac_cv_header_sys_time_h = yes; then
  AC_DEFINE([TIME_WITH_SYS_TIME],[1],[Define to 1 if you can safely include both <sys/time.h>
	     and <time.h>.  This macro is obsolete.])
fi
# End of obsolete code.


dnl Checks for library functions.
AC_CACHE_CHECK([return type of signal handlers],[ac_cv_type_signal],[AC_COMPILE_IFELSE(
[AC_LANG_PROGRAM([#include <sys/types.h>
#include <signal.h>
],
		 [return *(signal (0, 0)) (0) == 1;])],
		   [ac_cv_type_signal=int],
		   [ac_cv_type_signal=void])])
AC_DEFINE_UNQUOTED([RETSIGTYPE],[$ac_cv_type_signal],[Define as the return type of signal handlers
		    ('int' or 'void').])

AC_CHECK_FUNCS(strdup)

case "$target" in
    *-*-linux*)
        ARCH=linux
	;;
    *-*-bsdi*)
        ARCH=bsdi
	;;
    *-*-freebsd*)
        ARCH=freebsd
	;;
    *-*-solaris*)
        ARCH=solaris
	;;
    *)
        ARCH=other
	;;
esac

# Set runtime shared library paths as needed

if test $ARCH = linux -o $ARCH = freebsd -o $ARCH = bsdi; then
  AALIB_RLD_FLAGS="-Wl,-rpath,\${exec_prefix}/lib"
fi

if test $ARCH = solaris; then
  AALIB_RLD_FLAGS="-R\${exec_prefix}/lib"
fi


dnl Expand the cflags and libraries needed by apps using AALIB
AC_SUBST(AALIB_CFLAGS)
AC_SUBST(AALIB_LIBS)
AC_SUBST(AALIB_RLD_FLAGS)
AC_SUBST(XCURSES)

# Set conditional variables for shared and static library selection.
# These are not used in any Makefile.am but in sdl-config.in.
AM_CONDITIONAL([ENABLE_SHARED], [test "$enable_shared" = yes])
AM_CONDITIONAL([ENABLE_STATIC], [test "$enable_static" = yes])

echo "-----------------------------------------------------"
echo "CFLAGS  $CFLAGS"
echo "LDFLAGS $LIBS"
echo "AA-LIBD $AALIB_LIBS"
echo "CC      $CC"
echo "-----------------------------------------------------"
echo "Display drivers:"
if test  "$slang_driver" = true; then
  echo " slang"
  if test  "$slang_kbddriver_test" = yes; then
     AC_DEFINE([SLANG_KBDDRIVER],[1],[Define to 1 to enable the S-Lang keyboard driver.])
     slang_kbddriver=true
  fi
fi
if test  "$curses_driver" = true; then
  AC_DEFINE([CURSES_DRIVER],[1],[Define to 1 to enable the curses display driver.])
  echo " curses"
  if test  "$curses_kbddriver_test" = yes; then
     AC_DEFINE([CURSES_KBDDRIVER],[1],[Define to 1 to enable the curses keyboard driver.])
     curses_kbddriver=true
  fi
  if test  "$curses_kbddriver_test" = yes; then
    if test "x$ac_cv_func_mousemask" = xyes; then
     AC_DEFINE([CURSES_MOUSEDRIVER],[1],[Define to 1 to enable the curses mouse driver.])
     curses_mousedriver=true
    fi
  fi
fi
if test  "$linux_driver_test" = yes; then
 if test "x$system" = xLinux; then
   echo " Linux console"
   AC_DEFINE([LINUX_DRIVER],[1],[Define to 1 to enable the Linux console display driver.])
   AC_DEFINE([LINUX_KBDDRIVER],[1],[Define to 1 to enable the Linux console keyboard driver.])
 fi
fi
if test  "$x_driver" = true; then
  echo " x11"
  if test  "$x_kbddriver_test" = yes; then
     AC_DEFINE([X11_KBDDRIVER],[1],[Define to 1 to enable the X11 keyboard driver.])
     x_kbddriver=true
  fi
  if test  "$x_mousedriver_test" = yes; then
     AC_DEFINE([X11_MOUSEDRIVER],[1],[Define to 1 to enable the X11 mouse driver.])
     x_mousedriver=true
  fi
fi
echo "Keyboard drivers:"
if test  "$linux_driver_test" = yes; then
 if test "x$system" = xLinux; then
   echo " Linux console"
 fi
fi
if test  "$slang_kbddriver" = true; then
  echo " slang"
fi
if test  "$curses_kbddriver" = true; then
  echo " curses"
fi
if test  "$x_kbddriver" = true; then
  echo " X11"
fi
echo "Mouse drivers:"
if test  "$curses_mousedriver" = true; then
  echo " curses"
fi
if test  "$gpm_mousedriver" = true; then
  echo " gpm"
fi
if test  "$x_mousedriver" = true; then
  echo " X11"
fi
echo "-----------------------------------------------------"


AC_CONFIG_FILES([Makefile man/Makefile doc/Makefile src/Makefile aalib-config])
AC_CONFIG_COMMANDS([default],[chmod +x aalib-config],[])
AC_OUTPUT
