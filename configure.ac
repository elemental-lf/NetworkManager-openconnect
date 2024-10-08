AC_PREREQ(2.52)

AC_INIT(NetworkManager-openconnect,
        1.2.11,
        https://gitlab.gnome.org/GNOME/NetworkManager-openconnect/issues,
        NetworkManager-openconnect)
AM_INIT_AUTOMAKE([no-dist-gzip dist-xz])
AM_MAINTAINER_MODE

AC_CONFIG_MACRO_DIR([m4])

AC_CONFIG_HEADERS([config.h])

AC_USE_SYSTEM_EXTENSIONS

dnl
dnl Require programs
dnl
AC_PROG_CC
AM_PROG_CC_C_O
AC_PROG_INSTALL
AC_PROG_LIBTOOL
AC_PATH_PROG(GLIB_COMPILE_RESOURCES, glib-compile-resources)

AC_GNU_SOURCE

AC_CHECK_PROG([has_file], file, yes, no)
if test x$has_file = xno ; then
    AC_MSG_ERROR(["file" utility not found.])
fi

AC_CHECK_PROG([has_find], find, yes, no)
if test x$has_find = xno ; then
    AC_MSG_ERROR(["find" utility not found.])
fi

dnl
dnl Required headers
dnl
AC_HEADER_STDC
AC_CHECK_HEADERS(fcntl.h paths.h sys/ioctl.h sys/time.h syslog.h unistd.h)

dnl
dnl Checks for typedefs, structures, and compiler characteristics.
dnl
AC_TYPE_MODE_T
AC_TYPE_PID_T
AC_HEADER_TIME

dnl
dnl Checks for library functions.
dnl
AC_PROG_GCC_TRADITIONAL
AC_FUNC_MEMCMP
AC_CHECK_FUNCS(select socket uname)

GIT_SHA_RECORD(NM_GIT_SHA)

AC_SEARCH_LIBS([dlopen], [dl dld], [], [ac_cv_search_dlopen=])
AC_SUBST([DL_LIBS], "$ac_cv_search_dlopen")

dnl ensure that when the Automake generated makefile calls aclocal,
dnl it honours the $ACLOCAL_FLAGS environment variable
ACLOCAL_AMFLAGS="\${ACLOCAL_FLAGS}"
if test -n "$ac_macro_dir"; then
    ACLOCAL_AMFLAGS="-I $ac_macro_dir $ACLOCAL_AMFLAGS"
fi
AC_SUBST([ACLOCAL_AMFLAGS])

dnl
dnl GNOME support
dnl
AC_ARG_WITH(gnome, AS_HELP_STRING([--without-gnome], [Build NetworkManager-openconnect without GNOME support, e.g. vpn service only]), [], [with_gnome_specified=no])
AC_ARG_WITH(gtk4, AS_HELP_STRING([--with-gtk4], [Build NetworkManager-openconnect with libnma-gtk4 support]), [], [with_gtk4_specified=no])
AC_ARG_WITH(libnm-glib, AS_HELP_STRING([--with-libnm-glib], [Build NetworkManager-openconnect with libnm-glib compatibility (deprecated)]), [], [with_libnm_glib_specified=no])
if test "$with_libnm_glib_specified" != no -a "$with_libnm_glib" != no; then
	if test "$with_gnome_specified" != no -a "$with_gnome" == no; then
		AC_MSG_ERROR(Building --with-libnm-glib conflicts with --without-gnome)
	fi
fi
if test "$with_gnome" != no; then
	with_gnome=yes
fi
if test "$with_gtk4_specified" == no; then
	with_gtk4=no
fi
if test "$with_gtk4" != yes; then
	with_gtk4=no
fi
if test "$with_libnm_glib_specified" == no; then
	with_libnm_glib=no
fi
if test "$with_libnm_glib" != yes; then
	with_libnm_glib=no
fi
AM_CONDITIONAL(WITH_GNOME, test "$with_gnome" != no)
AM_CONDITIONAL(WITH_GTK4, test "$with_gtk4" != no)
AM_CONDITIONAL(WITH_LIBNM_GLIB, test "$with_libnm_glib" != no)

AC_ARG_ENABLE(absolute-paths, AS_HELP_STRING([--enable-absolute-paths], [Use absolute paths to in .name files. Useful for development. (default is no)]))

GETTEXT_PACKAGE=NetworkManager-openconnect
AC_SUBST(GETTEXT_PACKAGE)
AC_DEFINE_UNQUOTED(GETTEXT_PACKAGE,"$GETTEXT_PACKAGE", [Gettext package])

IT_PROG_INTLTOOL([0.35])
AM_GLIB_GNU_GETTEXT

PKG_CHECK_MODULES(GLIB, glib-2.0 >= 2.34)
GLIB_CFLAGS="$GLIB_CFLAGS -DGLIB_VERSION_MIN_REQUIRED=GLIB_VERSION_2_34"
GLIB_CFLAGS="$GLIB_CFLAGS -DGLIB_VERSION_MAX_ALLOWED=GLIB_VERSION_2_34"

PKG_CHECK_MODULES(GMODULE, gmodule-2.0)
PKG_CHECK_MODULES(LIBXML, libxml-2.0)

if test x"$with_gnome" != xno || test x"$with_gtk4" != xno; then
	PKG_CHECK_MODULES(LIBSECRET, libsecret-1 >= 0.18)
fi

if test x"$with_gnome" != xno; then
	PKG_CHECK_MODULES(GTK, gtk+-3.0 >= 3.12)
	GTK_CFLAGS="$GTK_CFLAGS -DGDK_VERSION_MIN_REQUIRED=GDK_VERSION_3_12"
	GTK_CFLAGS="$GTK_CFLAGS -DGDK_VERSION_MAX_ALLOWED=GDK_VERSION_3_12"
	PKG_CHECK_MODULES(LIBNMA, libnma >= 1.2.0)

	PKG_CHECK_MODULES(GCR, gcr-3 >= 3.4)
	GCR_CFLAGS="$GCR_CFLAGS -DGCR_API_SUBJECT_TO_CHANGE"

	if test x"$with_libnm_glib" != xno; then
		PKG_CHECK_MODULES(LIBNM_GLIB,
			NetworkManager >= 1.2.0
			libnm-util >= 1.2.0
			libnm-glib >= 1.2.0
			libnm-glib-vpn >= 1.2.0);

		LIBNM_GLIB_CFLAGS="$LIBNM_GLIB_CFLAGS -DNM_VERSION_MIN_REQUIRED=NM_VERSION_1_2"
		LIBNM_GLIB_CFLAGS="$LIBNM_GLIB_CFLAGS -DNM_VERSION_MAX_ALLOWED=NM_VERSION_1_4"
	fi
fi

if test x"$with_gtk4" != xno; then
	PKG_CHECK_MODULES(GTK4, gtk4 >= 4.0)
	GTK4_CFLAGS="$GTK4_CFLAGS -DGDK_VERSION_MIN_REQUIRED=GDK_VERSION_4_0"
	GTK4_CFLAGS="$GTK4_CFLAGS -DGDK_VERSION_MAX_ALLOWED=GDK_VERSION_4_0"
	PKG_CHECK_MODULES(LIBNMA_GTK4, libnma-gtk4 >= 1.8.33)
fi

PKG_CHECK_MODULES(WEBKIT, webkit2gtk-4.1, [wekbit=4.1],
			  [PKG_CHECK_MODULES(WEBKIT, webkit2gtk-4.0,
			  [webkit=4.0], AC_MSG_ERROR(Neither webkit2gtk-4.0 nor wekit2gtk-4.1 found))])

PKG_CHECK_MODULES(LIBNM, libnm >= 1.2.0)
LIBNM_CFLAGS="$LIBNM_CFLAGS -DNM_VERSION_MIN_REQUIRED=NM_VERSION_1_2"
LIBNM_CFLAGS="$LIBNM_CFLAGS -DNM_VERSION_MAX_ALLOWED=NM_VERSION_1_4"

AC_ARG_WITH(authdlg, AS_HELP_STRING([--without-authdlg], [Build NetworkManager-openconnect without authentication dialog]), [], [with_authdlg_specified=no])
if test x"$with_authdlg_specified" == xno; then
	with_authdlg="$with_gnome"
else
	if test x"$with_authdlg" != xno; then
		if test x"$with_gnome" == xno; then
			AC_MSG_ERROR(Building --with-authdlg requires --with-gnome)
		fi
		with_authdlg=yes
	fi
fi
if test x"$with_authdlg" != xno; then
	PKG_CHECK_MODULES(OPENCONNECT, openconnect >= 3.02)
fi
AM_CONDITIONAL(WITH_AUTHDLG, test x"$with_authdlg" != xno)

NM_VPN_SERVICE_DIR=`$PKG_CONFIG --define-variable prefix='\${prefix}' --variable vpnservicedir libnm`
AC_SUBST(NM_VPN_SERVICE_DIR)

NM_COMPILER_WARNINGS

NM_PLUGIN_DIR="$libdir/NetworkManager"
AC_SUBST(NM_PLUGIN_DIR)
if test x"$enable_absolute_paths" == x"yes"; then
	NM_PLUGIN_DIR_NAME_FILE="$NM_PLUGIN_DIR/"
else
	enable_absolute_paths=no
	NM_PLUGIN_DIR_NAME_FILE=""
fi
AC_SUBST(NM_PLUGIN_DIR_NAME_FILE)

dnl
dnl Distribution version string
dnl
AC_ARG_WITH(dist-version, AS_HELP_STRING([--with-dist-version=<dist-version>], [Define the custom version (like distribution package name and revision)]), ac_distver=$withval, ac_distver="")
if ! test x"$ac_distver" = x""; then
  AC_DEFINE_UNQUOTED(DIST_VERSION, "$ac_distver", [Define the distribution version string])
fi

AC_CONFIG_FILES([
Makefile
po/Makefile.in
])
AC_OUTPUT

echo ""
echo "Build configuration: "
echo "  --with-gnome=$with_gnome"
echo "  --with-gtk4=$with_gtk4"
echo "  --with-libnm-glib=$with_libnm_glib"
echo "  --with-authdlg=$with_authdlg"
echo "  --enable-absolute-paths=$enable_absolute_paths"
echo "  --enable-more-warnings=$set_more_warnings"
