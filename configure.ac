AC_PREREQ([2.58])
dnl PLEASE remove the nano when doing a release
AC_INIT(gst-rtmp,0.1.0.1)

AS_NANO([GST_RTMP_UNRELEASED=no],[GST_RTMP_UNRELEASED=yes])

AC_CANONICAL_HOST([])
AM_INIT_AUTOMAKE(1.6)

GST_API_VERSION=1.0
AC_SUBST(GST_API_VERSION)

AC_CONFIG_HEADERS(config.h)

dnl CURRENT, REVISION, AGE
dnl - library source changed -> increment REVISION
dnl - interfaces added/removed/changed -> increment CURRENT, REVISION = 0
dnl - interfaces added -> increment AGE
dnl - interfaces removed -> AGE = 0
GST_RTMP_LIBVERSION="0:0:0"
AC_SUBST(GST_RTMP_LIBVERSION)

AC_LIBTOOL_WIN32_DLL
AC_PROG_LIBTOOL
AM_PROG_AS
AC_PROG_CXX
AM_PROG_CC_C_O
AC_SYS_LARGEFILE
LT_PATH_NM

dnl allow for different autotools
AS_AUTOTOOLS_ALTERNATE

AC_CONFIG_SRCDIR([rtmp/rtmpserver.c])

AC_CONFIG_MACRO_DIR([m4])
ACLOCAL_AMFLAGS="-I m4 $ACLOCAL_AMFLAGS"
AC_SUBST(ACLOCAL_AMFLAGS)

dnl set up gettext
dnl the version check needs to stay here because autopoint greps for it
#AM_GNU_GETTEXT_VERSION([0.11.5])
#AM_GNU_GETTEXT([external])

AC_CHECK_LIBM
AC_SUBST(LIBM)

AS_COMPILER_FLAG(-Wall, GST_RTMP_CFLAGS="$GST_RTMP_CFLAGS -Wall")
if test "x$GST_RTMP_UNRELEASED" = "xyes"
then
  AS_COMPILER_FLAG(-Werror, GST_RTMP_CFLAGS="$GST_RTMP_CFLAGS -Werror")
fi

#AS_COMPILER_FLAG(-O3, CFLAGS=`echo $CFLAGS | sed s/O2/O3/`)
#AS_COMPILER_FLAG(-O3, CXXFLAGS=`echo $CXXFLAGS | sed s/O2/O3/`)

# Enable severe pain
#AS_COMPILER_FLAGS(COMPANY_CFLAGS,"-Wextra -Wno-missing-field-initializers -Wno-unused-parameter -Wold-style-definition -Wdeclaration-after-statement -Wmissing-declarations -Wmissing-prototypes -Wredundant-decls -Wmissing-noreturn -Wshadow -Wpointer-arith -Wcast-align -Wwrite-strings -Winline -Wformat-nonliteral -Wformat-security -Wswitch-enum -Wswitch-default")
#COMPANY_CFLAGS="-Wextra -Wno-missing-field-initializers -Wno-unused-parameter -Wold-style-definition -Wdeclaration-after-statement -Wmissing-declarations -Wmissing-prototypes -Wredundant-decls -Wmissing-noreturn -Wshadow -Wpointer-arith -Wcast-align -Wwrite-strings -Winline -Wformat-nonliteral -Wformat-security -Wswitch-enum -Wswitch-default"
#GST_RTMP_CFLAGS="$GST_RTMP_CFLAGS $COMPANY_CFLAGS"


##################################################
# Check for gtk-doc.
##################################################

if test "x$cross_compiling" = "xyes" ; then
  enable_gtk_doc=no
fi
GTK_DOC_CHECK([1.0])


########################
# GStreamer stuff
########################

PKG_PROG_PKG_CONFIG()
AC_ARG_WITH(gst-api-version,
  AC_HELP_STRING([--with-gst-api-version],[0.10,1.0,auto (default auto)]),
    [], [with_gst_api_version=auto])
case "${with_gst_api_version}" in
  0.10|1.0)
    GST_API_VERSION="${with_gst_api_version}"
    ;;
  auto)
    if ${PKG_CONFIG} --exists gstreamer-1.0 ; then
      GST_API_VERSION=1.0
    elif ${PKG_CONFIG} --exists gstreamer-0.10 ; then
      GST_API_VERSION=0.10
    else
      GST_API_VERSION=1.0
    fi
    ;;
  *)
    echo "Bad gst-api-version: $with_gst_api_version"
    ;;
esac

dnl *** check for arguments to configure ***

GLIB_REQ=2.26.0
PKG_CHECK_MODULES(GLIB, glib-2.0 >= $GLIB_REQ, HAVE_GLIB=yes, HAVE_GLIB=no)
if test "$HAVE_GLIB" != yes ; then
  echo "glib-2.0 >= $GLIB_REQ is required to build"
  exit 1
fi

PKG_CHECK_MODULES(GST, gstreamer-$GST_API_VERSION gstreamer-base-$GST_API_VERSION gstreamer-video-$GST_API_VERSION gstreamer-audio-$GST_API_VERSION gio-2.0,
    HAVE_GST=yes, HAVE_GST=no)
if test "$HAVE_GST" != yes ; then
  echo GStreamer and gst-plugns-base are needed to build
  exit 1
fi
AC_SUBST(GST_CFLAGS)
AC_SUBST(GST_LIBS)

LIBSOUP_REQ=2.38.0
PKG_CHECK_MODULES(SOUP, libsoup-2.4 > LIBSOUP_REQ, HAVE_SOUP=yes, HAVE_SOUP=no)
if test "$HAVE_SOUP" != yes ; then
  echo libsoup is needed to build
  exit 1
fi
AC_SUBST(SOUP_CFLAGS)
AC_SUBST(SOUP_LIBS)

GST_ALL_LDFLAGS="-no-undefined"
AC_SUBST(GST_ALL_LDFLAGS)

AM_CONDITIONAL(HAVE_FALSE, false)

GST_RTMP_CFLAGS="$GST_RTMP_CFLAGS -I\$(top_srcdir)"
AC_SUBST(GST_RTMP_CFLAGS)

GST_RTMP_LIBS="\$(top_builddir)/rtmp/libgstrtmp-$GST_API_VERSION.la"
AC_SUBST(GST_RTMP_LIBS)


AS_HOST_DEFINES()

AC_CONFIG_FILES([
Makefile 
doc/Makefile
rtmp/Makefile
pkgconfig/Makefile
pkgconfig/gst-rtmp-uninstalled.pc
pkgconfig/gst-rtmp.pc
plugins/Makefile
tools/Makefile
doc/version.entities
])
AC_OUTPUT

#case "$host_os" in
#  mingw32*)
#    sed 's/^deplibs_check_method=.*/deplibs_check_method="pass_all"/' libtool >libtool.tmp && mv libtool.tmp libtool
#    ;;
#esac

