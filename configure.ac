dnl Process this file with autoconf to produce a configure script.

AC_PREREQ([2.64])

dnl To set also in config.h.mingw
dnl defines ($PACKAGE_NAME, $PACKAGE_VERSION, etc.)
AC_INIT([EasyTAG], [2.1.8], [https://bugzilla.gnome.org/enter_bug.cgi?product=easytag], [easytag], [http://projects.gnome.org/easytag/])

AC_CONFIG_AUX_DIR([build-aux])
AC_CONFIG_HEADERS([config.h])
AC_CONFIG_SRCDIR([src/easytag.h])
AC_CONFIG_MACRO_DIR([m4])

dnl -------------------------------
dnl Init automake
dnl -------------------------------
AM_INIT_AUTOMAKE([1.11 -Wall -Wno-portability check-news no-define dist-xz no-dist-gzip nostdinc subdir-objects])
AM_MAINTAINER_MODE([enable])
AM_SILENT_RULES([yes])

dnl -------------------------------
dnl Translation files.
dnl -------------------------------
IT_PROG_INTLTOOL([0.50.0])
AC_SUBST([GETTEXT_PACKAGE], [$PACKAGE_TARNAME])
AC_DEFINE_UNQUOTED([GETTEXT_PACKAGE], ["$GETTEXT_PACKAGE"], [Package name for gettext])

dnl -------------------------------
dnl Checks for programs.
dnl -------------------------------
AC_PROG_CC
AM_PROG_CC_C_O
AC_PROG_CXX
AC_PROG_CXXCPP
AC_PROG_SED
PKG_PROG_PKG_CONFIG([0.24])

dnl Man page, generated with xsltproc from DocBook XML.
AC_ARG_ENABLE([man],
    [AS_HELP_STRING([--disable-man], [Disable building the man page])])

AS_IF([test "x$enable_man" != "xno"],
    [AC_PATH_PROG([XSLTPROC], [xsltproc])
     JH_CHECK_XML_CATALOG([-//OASIS//DTD DocBook XML V4.4//EN],
         [DocBook XML DTD V4.4], [have_docbookdtd=yes], [have_docbookdtd=no])
     JH_CHECK_XML_CATALOG(
         [http://docbook.sourceforge.net/release/xsl/current/html/refentry.xsl],
         [DocBook XSL Stylesheets], [have_docbookxsl=yes],
         [have_docbookxsl=no])
     AS_IF([test "x$ac_cv_path_XSLTPROC" != "x" \
         -a "x$have_docbookdtd" = "xyes" -a "x$have_docbookxsl" = "xyes"],
         [have_manutils=yes],
         [AS_IF([test "x$enable_man" = "xyes"],
             [AC_MSG_ERROR(
                 [man page generation requested but required utilities were not found])])
          have_manutils=no])],
    [have_manutils=no])

AM_CONDITIONAL([ENABLE_MAN], [test "x$have_manutils" = "xyes"])

dnl Check for testing utilities.
AC_ARG_ENABLE([tests],
              [AS_HELP_STRING([--disable-tests], [Disable support for tests run during make check])])

AS_IF([test "x$enable_tests" != "xno"],
      [AC_PATH_PROG([DESKTOP_FILE_VALIDATE], [desktop-file-validate])
       AS_IF([test "x$ac_cv_path_DESKTOP_FILE_VALIDATE" != "xyes"],
             [have_desktop_file_validate=yes],
             [have_desktop_file_validate=no])],
      [have_desktop_file_validate=no])

AS_IF([test "x$have_desktop_file_validate" = "xyes"],
      [testing_utilities=yes],
      [testing_utilities=no
       AS_IF([test "x$enable_tests" = "xyes"],
             [AC_MSG_ERROR([tests were requested but the required utilities were not found])])])

AM_CONDITIONAL([EASYTAG_ENABLE_TESTS], [test "x$testing_utilities" = "xyes"])

dnl -------------------------------
dnl Handle cross-compiling with mingw32.
dnl -------------------------------
AC_CANONICAL_HOST
AC_MSG_CHECKING([whether to build with mingw32 for a Windows host])
AS_CASE([$host_os],
        [*mingw*], [windows_host=yes],
        [windows_host=no])
AC_MSG_RESULT([$windows_host])

AM_CONDITIONAL([WINDOWS_HOST], [test "x$windows_host" = "xyes"])

dnl -------------------------------
dnl Configure switches.
dnl -------------------------------
AC_ARG_ENABLE([mp3],
              [AS_HELP_STRING([--disable-mp3], [Disable support for MP3 files (default=auto)])])

AC_ARG_ENABLE([id3v23],
              [AS_HELP_STRING([--disable-id3v23], [Disable support for writing id3v2.3 tags (default=enabled)])])

AC_ARG_ENABLE([ogg],
              [AS_HELP_STRING([--disable-ogg], [Disable support for Ogg Vorbis files (default=auto)])])

AC_ARG_ENABLE([speex],
              [AS_HELP_STRING([--disable-speex], [Disable support for Ogg Speex files (default=auto)])])

AC_ARG_ENABLE([flac],
              [AS_HELP_STRING([--disable-flac], [Disable support for FLAC files (default=auto)])])

AC_ARG_ENABLE([mp4],
              [AS_HELP_STRING([--disable-mp4], [Disable support for MP4 files (default=auto)])])

AC_ARG_ENABLE([wavpack],
              [AS_HELP_STRING([--disable-wavpack], [Disable support for Wavpack files (default=auto)])])


dnl -------------------------------
dnl Checks for libraries.
dnl -------------------------------

dnl GTK+ libraries
GTK2_DEPS="gtk+-2.0 >= 2.24.7"
GTK3_DEPS="gtk+-3.0 >= 3.2.1"
PKG_CHECK_EXISTS([$GTK2_DEPS], [have_gtk2=yes], [have_gtk2=no])
PKG_CHECK_EXISTS([$GTK3_DEPS], [have_gtk3=yes], [have_gtk3=no])

AC_ARG_WITH([gtk3],
            [AS_HELP_STRING([--with-gtk3], [Enable experimental GTK+ 3 support (and disable GTK+ 2 support)])])

AC_MSG_CHECKING([whether to use GTK+ 3])
AS_IF([test "x$have_gtk3" = "xyes" -a "x$have_gtk2" = "xno" -a "x$with_gtk3" != "xyes"],
      [AC_MSG_WARN([forcing GTK+ 3 support, as GTK+ 2 was not found])
       with_gtk3=yes])
AS_IF([test "x$with_gtk3" = "xyes"],
      [AC_MSG_RESULT([yes])
       AC_MSG_WARN([GTK+ 3 support is currently experimental!])
       GTK_DEPS="$GTK3_DEPS"],
      [AC_MSG_RESULT([no])
       GTK_DEPS="$GTK2_DEPS"])
PKG_CHECK_MODULES([GTK], [$GTK_DEPS])


dnl Patch from Ben Taylor to fix a Solaris bug (for CDDB)
AC_CHECK_LIB([socket], [recv])


dnl ################################################
dnl # Ogg Vorbis libraries
dnl ################################################

OGG_DEPS="ogg >= 1.0 vorbis >= 1.0.1 vorbisfile"
AS_IF([test "x$enable_ogg" != "xno"],
      [PKG_CHECK_EXISTS([$OGG_DEPS], [have_ogg=yes], [have_ogg=no])],
      [have_ogg=no])

AS_IF([test "x$have_ogg" != "xno"],
      [AC_DEFINE([ENABLE_OGG], [], [Define for Ogg Vorbis support])],
      [OGG_DEPS=""
       AS_IF([test "x$enable_ogg" = "xyes"],
             [AC_MSG_ERROR([OGG Vorbis support requested but required dependencies ($OGG_DEPS) not found])])])

dnl ################################################
dnl # libSpeex library
dnl ################################################
dnl check for system libspeex

SPEEX_DEPS="speex"
AS_IF([test "x$enable_speex" != "xno"],
      [PKG_CHECK_EXISTS([$SPEEX_DEPS], [have_speex=yes], [have_speex=no])],
      [have_speex=no])

AS_IF([test "x$have_speex" != "xno"],
      [AC_DEFINE([ENABLE_SPEEX], [], [Define for Speex support])],
      [SPEEX_DEPS=""
       AS_IF([test "x$enable_speex" = "xyes"],
             [AC_MSG_ERROR([OGG Speex support requested but required dependencies ($SPEEX_DEPS) not found])])])

dnl ################################################
dnl # libFLAC library
dnl ################################################
dnl check for system libflac

FLAC_DEPS="flac >= 1.1.4"
AS_IF([test "x$enable_flac" != "xno"],
      [PKG_CHECK_EXISTS([$FLAC_DEPS], [have_flac=yes], [have_flac=no])],
      [have_flac=no])

AS_IF([test "x$have_flac" != "xno"],
      [AC_DEFINE([ENABLE_FLAC], [], [Define for FLAC support])],
      [FLAC_DEPS=""
       AS_IF([test "x$enable_flac" = "xyes"],
             [AC_MSG_ERROR([FLAC support requested but required dependencies ($FLAC_DEPS) not found])])])

dnl ################################################
dnl # libid3tag and Id3lib libraries
dnl ################################################
ID3TAG_DEPS="id3tag"
AS_IF([test "x$enable_mp3" != "xno"],
      [PKG_CHECK_EXISTS([$ID3TAG_DEPS], [have_mp3=yes], [have_mp3=no])],
      [have_mp3=no])

AS_IF([test "x$have_mp3" != "xno"],
      [AC_DEFINE([ENABLE_MP3], [], [Define for MP3 support])],
      [ID3TAG_DEPS=""
       AS_IF([test "x$enable_mp3" = "xyes"],
             [AC_MSG_ERROR([MP3 support requested but required dependencies ($ID3TAG_DEPS) not found])])])

AS_IF([test "x$have_mp3" = "xyes" -a "x$enable_id3v23" != "xno"],
      dnl Check which libs are required by id3lib, libid3.la is fucked up
      [LIBS_SAVE="$LIBS"
       AC_SEARCH_LIBS([ID3Tag_Link], ["id3" "id3 -lstdc++" "id3 -lz" "id3 -lz -lstdc++"], [have_id3lib=yes], [have_id3lib=no])
        
       dnl expected version for cross compiling
       ID3LIB_MAJOR=3
       ID3LIB_MINOR=8
       ID3LIB_PATCH=0

       AC_MSG_CHECKING([the id3lib version])
       AC_RUN_IFELSE([AC_LANG_PROGRAM([[
#include <id3.h>
#include <stdio.h>
]],                                   [[
    FILE *output;
    output=fopen("conftest.id3","w");
    fprintf(output,"ID3LIB_MAJOR=%d\nID3LIB_MINOR=%d\nID3LIB_PATCH=%d\n",ID3LIB_MAJOR_VERSION,ID3LIB_MINOR_VERSION,ID3LIB_PATCH_VERSION);
    fclose(output);
    return 0;
]])], [. ./conftest.id3; AC_MSG_RESULT([${ID3LIB_MAJOR}.${ID3LIB_MINOR}.${ID3LIB_PATCH}])], [AC_MSG_ERROR([could not determine id3lib version])], [echo $ac_n "cross compiling; assuming ${ID3LIB_MAJOR}.${ID3LIB_MINOR}.${ID3LIB_PATCH} $ac_c"])
       LIBS="$LIBS_SAVE"
       AC_DEFINE_UNQUOTED([ID3LIB_MAJOR], [$ID3LIB_MAJOR], [id3lib major version])
       AC_DEFINE_UNQUOTED([ID3LIB_MINOR], [$ID3LIB_MINOR], [id3lib minor version])
       AC_DEFINE_UNQUOTED([ID3LIB_PATCH], [$ID3LIB_PATCH], [id3lib patch level])

       AC_SUBST([ID3LIB_LIBS], [$ac_cv_search_ID3Tag_Link])
       AC_DEFINE([ENABLE_ID3LIB], [], [Define for ID3v2.3 support])
       ID3LIB_VERSION="(id3lib-$ID3LIB_MAJOR.$ID3LIB_MINOR.$ID3LIB_PATCH)"],
      [have_id3lib=no])


dnl ################################################
dnl # taglib library
dnl ################################################

TAGLIB_DEPS="taglib_c >= 1.6.0"
AS_IF([test "x$enable_mp4" != "xno"],
      [PKG_CHECK_EXISTS([$TAGLIB_DEPS], [have_taglib=yes], [have_taglib=no])],
      [have_taglib=no])

AS_IF([test "x$have_taglib" != "xno"],
      [AC_DEFINE([ENABLE_MP4], [], [Define for taglib MP4 support])],
      [TAGLIB_DEPS=""
       AS_IF([test "x$enable_mp4" = "xyes"],
             [AC_MSG_ERROR([Taglib MP4 support requested but required dependencies ($TAGLIB_DEPS) not found])])])

dnl ################################################
dnl # WavPack library
dnl ################################################
WAVPACK_DEPS="wavpack >= 4.40"
AS_IF([test "x$enable_wavpack" != "xno"],
      [PKG_CHECK_EXISTS([$WAVPACK_DEPS], [have_wavpack=yes], [have_wavpack=no])],
      [have_wavpack=no])

AS_IF([test "x$have_wavpack" != "xno"],
      [AC_DEFINE([ENABLE_WAVPACK], [], [Define for Wavpack support])],
      [WAVPACK_DEPS=""
       AS_IF([test "x$enable_wavpack" = "xyes"],
             [AC_MSG_ERROR([Wavpack support requested but required dependencies ($WAVPACK_DEPS) not found])])])

dnl Check the pkg-config dependencies
GIO_DEPS="gio-2.0 >= 2.32.0" dnl For g_file_new_tmp()
PKG_CHECK_MODULES([EASYTAG], [$GIO_DEPS $GTK_DEPS $OGG_DEPS $SPEEX_DEPS $FLAC_DEPS $ID3TAG_DEPS $TAGLIB_DEPS $WAVPACK_DEPS])

dnl Check for winsock
AC_SEARCH_LIBS([gethostbyname], [nsl socket], [],
    [saved_LIBS=$LIBS
     LIBS="-lws2_32 $LIBS"
     AC_MSG_CHECKING([for gethostbyname in ws2_32])
     AC_LINK_IFELSE([AC_LANG_PROGRAM([[#include <winsock2.h>]],
        [[gethostbyname ("");]])],
        [have_ws2=yes], [have_ws2=no LIBS=$saved_LIBS])
     AC_MSG_RESULT([$have_ws2])])

dnl Check that the compiler accepts the given warning flags.
warning_flags="-Wall -Wstrict-prototypes -Wnested-externs -Werror=missing-prototypes -Werror=implicit-function-declaration -Werror=pointer-arith -Werror=init-self -Werror=format-security -Werror=format=2 -Werror=missing-include-dirs -Werror=declaration-after-statement"

realsave_CFLAGS="$CFLAGS"
for option in $warning_flags; do
    save_CFLAGS="$CFLAGS"
    CFLAGS="$CFLAGS $option"
	AC_MSG_CHECKING([whether the compiler understands $option])
	AC_COMPILE_IFELSE([AC_LANG_PROGRAM], [has_option=yes], [has_option=no])
	CFLAGS="$save_CFLAGS"
	AC_MSG_RESULT([$has_option])
	AS_IF([test $has_option = yes],
	      [tested_warning_flags="$tested_warning_flags $option"])
	unset has_option
	unset save_CFLAGS
done
unset option
CFLAGS="$realsave_CFLAGS"

AC_SUBST([WARN_CFLAGS], ["$tested_warning_flags"])
AC_SUBST([DEPRECATED_CPPFLAGS], ["-DG_DISABLE_SINGLE_INCLUDES -DGTK_DISABLE_SINGLE_INCLUDES -DG_DISABLE_DEPRECATED -DGDK_DISABLE_DEPRECATED -DGTK_DISABLE_DEPRECATED -DGSEAL_ENABLE"])

dnl To enable 'gprof' profiling
dnl CFLAGS="$CFLAGS -pg"


dnl -------------------------------
dnl Checks for header files.
dnl -------------------------------
AC_HEADER_DIRENT


dnl -------------------------------
dnl Checks for typedefs, structures, and compiler characteristics.
dnl -------------------------------
AC_TYPE_PID_T
AC_TYPE_SIZE_T


dnl -------------------------------
dnl Checks for library functions.
dnl -------------------------------
AC_CHECK_FUNCS([ftruncate gettimeofday mkstemp truncate])

AC_CONFIG_FILES([ Makefile
                  doc/version.xml
                  po/Makefile.in
               ])

dnl ------------------------------
dnl Check if we're building for Mac.
dnl ------------------------------
PKG_CHECK_MODULES(GTKOSXAPPLICATION, gtk-mac-integration, [have_mac=yes], [have_mac=no])
if test "x$have_mac" = xyes; then
    AC_DEFINE([GTKOSXAPPLICATION],[1],[Defined if app is GtkOSXApplication])
fi

AC_OUTPUT


echo 
echo Configuration for $PACKAGE_NAME $PACKAGE_VERSION :
echo --------------------------------
echo 
echo Source code location ....: $srcdir
echo Host System Type ........: $host
echo Preprocessor ............: $CC $CPPFLAGS
echo Deprecation defines .....: $DEPRECATED_CPPFLAGS
echo Compiler ................: $CC $CFLAGS
echo Compiler warnings .......: $WARN_CFLAGS
echo Linker ..................: $CC $LDFLAGS $LIBS
echo GTK version .............: `$PKG_CONFIG --modversion $GTK_DEPS`
echo MP3 file support ........: $have_mp3
echo ID3v2.3 tags support ....: $have_id3lib $ID3LIB_VERSION
echo Ogg Vorbis file support .: $have_ogg
echo Ogg Speex file support ..: $have_speex
echo FLAC file support .......: $have_flac
echo MP4 file support ........: $have_taglib
echo WavPack support .........: $have_wavpack
echo NLS/gettext .............: $USE_NLS
echo Mac support .............: $have_mac
echo Tests during make check .: $testing_utilities
echo Install prefix ..........: $prefix
echo 
echo Now type 'make' to build $PACKAGE_NAME $PACKAGE_VERSION,
echo and then 'make install' for installation.
echo
