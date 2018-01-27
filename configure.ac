dnl require autoconf 2.60 (AS_ECHO/AS_ECHO_N)
AC_PREREQ([2.60])
AC_LANG_PUSH([C++])
define(_CLIENT_VERSION_MAJOR, 3)
define(_CLIENT_VERSION_MINOR, 1)
define(_CLIENT_VERSION_REVISION, 1)
define(_CLIENT_VERSION_BUILD, 2)
AC_INIT([B3],
  [_CLIENT_VERSION_MAJOR._CLIENT_VERSION_MINOR._CLIENT_VERSION_REVISION],
  [https://github.com/B3-CoinV2/issues],
  [b3coin],
  [https://b3coin.io/])

AC_CONFIG_AUX_DIR([build-aux])
AC_CONFIG_MACRO_DIR([build-aux/m4])

B3_GUI_NAME=b3coin-qt

AC_CANONICAL_HOST

dnl faketime breaks configure and is only needed for make. Disable it here.
unset FAKETIME

dnl Automake init set-up and checks
AM_INIT_AUTOMAKE([no-define subdir-objects foreign])

dnl faketime messes with timestamps and causes configure to be re-run.
dnl --disable-maintainer-mode can be used to bypass this.
AM_MAINTAINER_MODE([enable])

dnl make the compilation flags quiet unless V=1 is used
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])

dnl Compiler checks (here before libtool).
if test "x${CXXFLAGS+set}" = "xset"; then
  CXXFLAGS_overridden=yes
else
  CXXFLAGS_overridden=no
fi
AC_PROG_CXX

dnl By default, libtool for mingw refuses to link static libs into a dll for
dnl fear of mixing pic/non-pic objects, and import/export complications. Since
dnl we have those under control, re-enable that functionality.
case $host in
  *mingw*)
     lt_cv_deplibs_check_method="pass_all"
  ;;
esac
dnl Require C++11 compiler (no GNU extensions)
dnl AX_CXX_COMPILE_STDCXX([11], [noext], [mandatory], [nodefault])
dnl Check if -latomic is required for <std::atomic>
dnl CHECK_ATOMIC

dnl Unless the user specified OBJCXX, force it to be the same as CXX. This ensures
dnl that we get the same -std flags for both.
m4_ifdef([AC_PROG_OBJCXX],[
if test "x${OBJCXX+set}" = "x"; then
  OBJCXX="${CXX}"
fi
AC_PROG_OBJCXX
])

dnl Libtool init checks.
LT_INIT([pic-only])

PKG_CHECK_MODULES([SSL], [libssl])
PKG_CHECK_MODULES([CRYPTO], [libcrypto])

# Hardening
AC_ARG_ENABLE([hardening],
  [AS_HELP_STRING(
    [--disable-hardening],
    [do not attempt to harden the resulting executables (default is to harden)])],
  [use_hardening=$enableval],
  [use_hardening=yes])

if test x$use_hardening != xno; then
  AX_CHECK_COMPILE_FLAG(
    [-fno-stack-protector],
    [HARDENED_CXXFLAGS="$HARDENED_CXXFLAGS -fno-stack-protector"])
  AX_CHECK_COMPILE_FLAG(
    [-fstack-protector-all],
    [HARDENED_CXXFLAGS="$HARDENED_CXXFLAGS -fstack-protector-all"])
  AX_CHECK_COMPILE_FLAG(
    [-Wstack-protector],
    [HARDENED_CXXFLAGS="$HARDENED_CXXFLAGS -Wstack-protector"])
  AX_CHECK_LINK_FLAG([[-Wl,-z,relro]],
    [HARDENED_LDFLAGS="$HARDENED_LDFLAGS -Wl,-z,relro"])
  AX_CHECK_LINK_FLAG([[-Wl,-z,now]],
    [HARDENED_LDFLAGS="$HARDENED_LDFLAGS -Wl,-z,now"])

  AX_CHECK_PREPROC_FLAG([-D_FORTIFY_SOURCE=2],[
    HARDENED_CPPFLAGS="$HARDENED_CPPFLAGS -D_FORTIFY_SOURCE=2"
  ])

  case $host in
    *mingw*)
       AC_CHECK_LIB([ssp],      [main],, AC_MSG_ERROR(lib missing))
    ;;
  esac
fi

# Enable wallet via configure
AC_ARG_ENABLE([wallet],
  [AS_HELP_STRING([--disable-wallet],
                  [disable wallet (enabled by default)])],
  [enable_wallet=$enableval],
  [enable_wallet=yes])

dnl Enable wallet variable
AC_MSG_CHECKING([if wallet should be enabled])
if test x$enable_wallet != xno; then
  AC_MSG_RESULT(yes)
  AC_DEFINE_UNQUOTED([ENABLE_WALLET],[1],[Define to 1 to enable wallet functions])
else
  AC_MSG_RESULT(no)
fi

# Enable debug
AC_ARG_ENABLE([debug],
    [AS_HELP_STRING([--enable-debug],
                    [use debug compiler flags and macros (default is no)])],
    [enable_debug=$enableval],
    [enable_debug=no])

if test "x$enable_debug" = xyes; then
    CPPFLAGS="$CPPFLAGS -DDEBUG -DDEBUG_LOCKORDER"
    if test "x$GCC" = xyes; then
        CFLAGS="$CFLAGS -g3 -O0"
    fi

    if test "x$GXX" = xyes; then
        CXXFLAGS="$CXXFLAGS -g3 -O0"
    fi
fi


dnl Boost
dnl Check for boost libs
AX_BOOST_BASE([1.47.0])
AX_BOOST_SYSTEM
AX_BOOST_FILESYSTEM
AX_BOOST_PROGRAM_OPTIONS
AX_BOOST_THREAD
AX_BOOST_CHRONO
BOOST_LIBS="$BOOST_LDFLAGS $BOOST_SYSTEM_LIB $BOOST_FILESYSTEM_LIB $BOOST_PROGRAM_OPTIONS_LIB $BOOST_THREAD_LIB $BOOST_CHRONO_LIB"

dnl pthread check
AX_PTHREAD

dnl Conditionals
AM_CONDITIONAL([ENABLE_WALLET], [test x$enable_wallet = xyes])
AM_CONDITIONAL([HARDEN],[test x$use_hardening = xyes])

dnl Output variables
AC_SUBST(BOOST_LIBS)
AC_SUBST(HARDENED_CXXFLAGS)
AC_SUBST(HARDENED_CPPFLAGS)
AC_SUBST(HARDENED_LDFLAGS)

dnl Makefile
AC_CONFIG_FILES([Makefile src/Makefile])
AC_OUTPUT
