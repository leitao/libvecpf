AC_INIT(vecpf, 1.0.0)
AC_PREREQ(2.60)

AC_CONFIG_SRCDIR([vecpf.c])
AC_CONFIG_HEADERS([config.h])

AC_USE_SYSTEM_EXTENSIONS([_GNU_SOURCE])

# We want to prevent building/configuration in the source directory.
if test "`cd $srcdir; /bin/pwd`" = "`/bin/pwd`"; then
        AC_MSG_ERROR([you must configure in a separate build directory])
fi

AC_ARG_ENABLE([vsx],
              AS_HELP_STRING([--enable-vsx],
                             [build with vsx support]),
[if test "$enableval" = yes; then
  AC_DEFINE([ENABLE_VSX], , [Enable VSX])
  AC_MSG_NOTICE(enabling support for VSX data types.)
  vsx="-mcpu=power7"

  saved_CFLAGS="$CFLAGS"
  CFLAGS="$CFLAGS -maltivec"

 # Check if the VSX instruction set works on this machine.  It will
 # determine whether we're cross-compiling and whether the test-suite
 # executes the VSX portions of the test.
  AC_CACHE_CHECK(for VSX instruction support,libvecpf_cv_native_vsx,
		[AC_RUN_IFELSE(AC_LANG_PROGRAM([[#include <altivec.h>]],
				[[__asm__ __volatile__("xsmaddadp 32, 32, 33" ::: "memory","cc");]]),
				libvecpf_cv_native_vsx=yes,
				libvecpf_cv_native_vsx=no,)])
  CFLAGS="$saved_CFLAGS"

  if test x$libvecpf_cv_native_vsx == xno; then
    AC_MSG_WARN([You have enabled VSX support but this machine does
		 not support the VSX instruction set.  VSX tests will not be
		 executed if you run the test suite.])
  else
    AC_MSG_NOTICE(The test suite will execute the VSX tests if run.)
  fi
fi
])

AC_SUBST(vsx)
# Use this to determine whether to run VSX tests in the test-suite.
AC_SUBST(libvecpf_cv_native_vsx)

AC_ARG_ENABLE([shared],
              AS_HELP_STRING([--enable-shared],
                             [build shared library @<:@default@:>@]),
              [case "$enableval" in
  yes)
       enable_shared=shared
       AC_MSG_NOTICE(enabling support for a shared library.);;
   no)
       enable_shared=
       AC_MSG_NOTICE(disabling support for a shared library.);;
    *)
       enable_shared=
       AC_MSG_NOTICE(enabling support for a shared library.);;
  esac],
              enable_shared=shared )
AC_SUBST(enable_shared)

AC_ARG_ENABLE([static],
              AS_HELP_STRING([--enable-static],
                             [build static library @<:@not default@:>@]),
              [case "$enableval" in
  yes)
       enable_static=static
       AC_MSG_NOTICE(enabling support for a static library.);;
   no)
       enable_static=
       AC_MSG_NOTICE(disabling support for a static library.);;
    *)
       enable_static=static
       AC_MSG_NOTICE(enabling support for a static library.);;
 esac],
              enable_static=)
AC_SUBST(enable_static)


AC_SUBST(sysheaders, [])
AC_ARG_WITH([headers], AC_HELP_STRING([--with-headers=PATH], [location of system headers to use (for example /usr/src/linux/include)
        @<:@default=compiler default@:>@]), [sysheaders=$withval], [sysheaders=”])

# Check for GLIBC 2.10 headers
have_glibc_2_10_headers=yes

AC_MSG_CHECKING([for printf-hook register_printf_type () in printf.h to verify GLIBC 2.10])
AC_TRY_COMPILE([#include "printf.h"],[
 static int foo;
 void foo_va (void *mem, va_list *ap)
 {
   return;
 }
 foo = register_printf_type (foo_va);
 ], [AC_MSG_RESULT([yes])], [AC_MSG_RESULT([no]); have_glibc_2_10_headers=no])

if test x$have_glibc_2_10_headers != xyes; then
  AC_MSG_ERROR([Building Libvecpf requires a GLIBC printf.h header that
		provides the register_printf_type () function which is
		available in GLIBC Version 2.10 or later.  The headers that
		this build is using don't provide this functionality.])
  exit 1;
fi

have_glibc_2_10=yes

AC_MSG_CHECKING([for register_printf_type() runtime support])
AC_TRY_LINK([#include "printf.h"],[
 static int foo;
 void foo_va (void *mem, va_list *ap)
 {
   return;
 }
 foo = register_printf_type (foo_va);
 ], [AC_MSG_RESULT([yes])], [AC_MSG_RESULT([no]); have_glibc_2_10=no])

if test x$have_glibc_2_10 != xyes; then
  AC_MSG_WARN([Executing Libdfp tests requires a GLIBC runtime that supports
	       the register_printf_type () function which is available in GLIBC
	       Version 2.10 or later.  The libc that this build is using
	       doesn't provide this functionality.  You will not be able to
	       successfully execute the test suite.])
fi

# It's ok to build the static lib with PIC.
picflag="-fpic"
AC_MSG_NOTICE(configuring with "$picflag")
AC_SUBST(picflag)

# Checks for programs.
AC_PROG_CC
AC_PROG_LN_S
AC_PROG_RANLIB

AC_DEFINE(_GNU_SOURCE)
AC_USE_SYSTEM_EXTENSIONS

AC_CHECK_HEADER(printf.h)
AC_CHECK_HEADER(altivec.h)
AC_CHECK_FUNCS(asprintf)

AC_CONFIG_FILES([Makefile])
AC_OUTPUT