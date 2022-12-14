# Process this file with autoconf to create configure.

AC_PREREQ([2.65])

# ====================
# Version informations
# ====================
m4_define([librevenge_version_major],[0])
m4_define([librevenge_version_minor],[0])
m4_define([librevenge_version_micro],[4])
m4_define([librevenge_version],[librevenge_version_major.librevenge_version_minor.librevenge_version_micro])

# =============
# Automake init
# =============
AC_INIT([librevenge],[librevenge_version])
AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_HEADER([config.h])
AM_INIT_AUTOMAKE([1.11 foreign dist-xz dist-bzip2])
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])
AC_LANG([C++])

# ===========================
# Find required base packages
# ===========================
m4_ifdef([AM_PROG_AR], [AM_PROG_AR])
AC_PROG_CC
AC_PROG_CPP
AC_PROG_CXX
AC_PROG_INSTALL
AC_PROG_LN_S
AC_PROG_MAKE_SET
LT_INIT([win32-dll disable-static pic-only])
AC_CANONICAL_HOST

# =================================
# Libtool/Version Makefile settings
# =================================
AC_SUBST(RVNG_MAJOR_VERSION, [librevenge_version_major])
AC_SUBST(RVNG_MINOR_VERSION, [librevenge_version_minor])
AC_SUBST(RVNG_MICRO_VERSION, [librevenge_version_micro])
AC_SUBST(RVNG_VERSION, [librevenge_version])
# AC_SUBST(LT_RELEASE, [librevenge_version_major.librevenge_version_minor])
LT_CURRENT=`expr 100 '*' librevenge_version_major + librevenge_version_minor`
# For 1.0.0 comment the first line and uncomment the second
LT_AGE=0
# LT_AGE=librevenge_version_minor
AC_SUBST(LT_CURRENT)
AC_SUBST(LT_REVISION, [librevenge_version_micro])
AC_SUBST(LT_AGE)

# ==========================
# Platform check for windows
# ==========================
AC_MSG_CHECKING([for native Win32])
AS_CASE([$host],
	[*-*-mingw*], [
		native_win32=yes
		LIBREVENGE_WIN32_RESOURCE=librevenge-win32res.lo
		LIBREVENGE_GENERATORS_WIN32_RESOURCE=librevenge-generators-win32res.lo
		LIBREVENGE_STREAM_WIN32_RESOURCE=librevenge-stream-win32res.lo
	], [
		native_win32=no
		LIBREVENGE_WIN32_RESOURCE=
		LIBREVENGE_GENERATORS_WIN32_RESOURCE=
		LIBREVENGE_STREAM_WIN32_RESOURCE=
	]
)
AC_MSG_RESULT([$native_win32])
AM_CONDITIONAL(OS_WIN32, [test "x$native_win32" = "xyes"])
AC_SUBST(LIBREVENGE_WIN32_RESOURCE)
AC_SUBST(LIBREVENGE_GENERATORS_WIN32_RESOURCE)
AC_SUBST(LIBREVENGE_STREAM_WIN32_RESOURCE)

AC_MSG_CHECKING([for Win32 platform in general])
AS_CASE([$host],
	[*-*-mingw*|*-*-cygwin*], [platform_win32=yes],
	[platform_win32=no]
)
AC_MSG_RESULT([$platform_win32])
AM_CONDITIONAL([PLATFORM_WIN32], [test "x$platform_win32" = "xyes"])

AS_IF([test "x$platform_win32" = "xyes"],
    [],
    [
        AC_MSG_CHECKING([for -fvisibility=hidden compiler flag])
        saved_CPPFLAGS="$CPPFLAGS"
        CPPFLAGS="$CPPFLAGS -fvisibility=hidden"
        AC_TRY_COMPILE([], [], [have_visibility=yes], [have_visibility=no])
        AC_MSG_RESULT([$have_visibility])
        CPPFLAGS="$saved_CPPFLAGS"
    ]
)
AM_CONDITIONAL([HAVE_VISIBILITY], [test "x$have_visibility" = "xyes"])

# =========================
# Platform check for darwin
# =========================
AC_MSG_CHECKING([for Darwin (Mac OS X) platform])
AS_CASE([$host],
	[*-*-darwin*], [platform_darwin=yes],
	[platform_darwin=no]
)
AC_MSG_RESULT([$platform_darwin])
AM_CONDITIONAL(PLATFORM_DARWIN, [test "x$platform_darwin" = "xyes"])

# ================
# Check for cflags
# ================
AC_ARG_ENABLE([werror],
	[AS_HELP_STRING([--disable-werror], [Treat all warnings as errors, usefull for development])],
	[enable_werror="$enableval"],
	[enable_werror=yes]
)
AS_IF([test x"$enable_werror" != "xno"], [
	CFLAGS="$CFLAGS -Werror"
	CXXFLAGS="$CXXFLAGS -Werror"
])
AC_ARG_ENABLE([weffc],
	[AS_HELP_STRING([--disable-weffc], [ Disable -Weffc++ warnings, usefull when using an old version of gcc or of boost])],
	[enable_weffc="$enableval"],
	[enable_weffc=yes]
)
AC_ARG_ENABLE([wparanoic],
	[AS_HELP_STRING([--enable-wparanoic], [Enable a lot of warnings...])],
	[enable_wparanoic="$enableval"],
	[enable_wparanoic=no]
)
# Courtesy of Glib: Ensure MSVC-compatible struct packing convention
# is used when compiling for Win32 with gcc.
AS_IF([test "x$native_win32" = "xyes"], [
	AC_CHECK_TOOL(WINDRES, windres)
	AS_IF([test x"$GCC" = xyes], [
		AC_MSG_CHECKING([how to get MSVC-compatible struct packing])
		AS_IF([test -z "$ac_cv_prog_CC"], [
			our_gcc="$CC"
		], [
			our_gcc="$ac_cv_prog_CC"
		])
		AS_IF([$our_gcc -v --help 2>/dev/null | grep ms-bitfields >/dev/null], [
			msnative_struct="-mms-bitfields"
			CFLAGS="$CFLAGS $msnative_struct"
			CXXFLAGS="$CXXFLAGS $msnative_struct"
			AC_MSG_RESULT([${msnative_struct}])
		], [
			AC_MSG_RESULT([no way])
			AC_MSG_WARN([produced libraries might be incompatible with MSVC-compiled code])
		])
	])
	CFLAGS="$CFLAGS -Wall -Wextra -pedantic"
	CXXFLAGS="$CXXFLAGS -Wall -Wextra -Wshadow -pedantic"
], [
	AS_IF([test x"$GCC" = xyes], [
		# Be tough with warnings and produce less careless code
		CFLAGS="$CFLAGS -Wall -Wextra -pedantic"
		CXXFLAGS="$CXXFLAGS -Wall -Wextra -pedantic -Wshadow -Wunused-variable"
		AS_IF([test x"$enable_weffc" != "xno"], [
			CXXFLAGS="$CXXFLAGS -Weffc++"
		])
		AS_IF([test x"$enable_wparanoic" != "xno"], [
			CXXFLAGS="$CXXFLAGS -Wcast-align -Wcast-qual -Wchar-subscripts"
			CXXFLAGS="$CXXFLAGS -Wcomment -Wconversion -Wdisabled-optimization"
			CXXFLAGS="$CXXFLAGS -Wfloat-equal -Wformat -Wformat=2"
			CXXFLAGS="$CXXFLAGS -Wformat-nonliteral -Wformat-security"
			CXXFLAGS="$CXXFLAGS -Wformat-y2k -Wimport -Winit-self -Winvalid-pch"
			CXXFLAGS="$CXXFLAGS -Wmissing-braces -Wmissing-field-initializers"
			CXXFLAGS="$CXXFLAGS -Wmissing-format-attribute -Wmissing-include-dirs"
			CXXFLAGS="$CXXFLAGS -Wmissing-noreturn -Wpacked -Wparentheses"
			CXXFLAGS="$CXXFLAGS -Wpointer-arith -Wredundant-decls -Wreturn-type"
			CXXFLAGS="$CXXFLAGS -Wsequence-point -Wsign-compare -Wstrict-aliasing"
			CXXFLAGS="$CXXFLAGS -Wstrict-aliasing=2 -Wswitch -Wswitch-default"
			CXXFLAGS="$CXXFLAGS -Wswitch-enum -Wtrigraphs -Wunknown-pragmas -Wunused"
			CXXFLAGS="$CXXFLAGS -Wunused-function -Wunused-label -Wunused-parameter"
			CXXFLAGS="$CXXFLAGS -Wunused-value -Wvariadic-macros"
			CXXFLAGS="$CXXFLAGS -Wvolatile-register-var -Wwrite-strings"
		])
	])
])

# ============
# Debug switch
# ============
AC_ARG_ENABLE([debug],
	[AS_HELP_STRING([--enable-debug], [Turn on debugging])],
	[enable_debug="$enableval"],
	[enable_debug=no]
)
AS_IF([test "x$enable_debug" = "xyes"], [
	DEBUG_CXXFLAGS="-DDEBUG -g"
	CXXFLAGS="$CXXFLAGS -O0"
	CFLAGS="$CFLAGS -O0"
], [
	DEBUG_CXXFLAGS="-DNDEBUG"
])
AC_SUBST(DEBUG_CXXFLAGS)

# ==================
# Find boost headers
# ==================
AC_CHECK_HEADERS(
		boost/algorithm/string.hpp \
		boost/archive/iterators/base64_from_binary.hpp \
		boost/archive/iterators/binary_from_base64.hpp \
		boost/archive/iterators/remove_whitespace.hpp \
		boost/archive/iterators/transform_width.hpp \
		boost/lexical_cast.hpp \
		boost/range/iterator_range.hpp \
		boost/scoped_ptr.hpp \
		boost/shared_ptr.hpp \
		boost/spirit/include/classic.hpp,
	[],
	[AC_MSG_ERROR([Required boost headers not found.])],
	[]
)

# ==========
# Unit tests
# ==========
AC_ARG_ENABLE([tests],
    [AS_HELP_STRING([--enable-tests], [Build and run unit tests])],
    [enable_tests="$enableval"],
    [enable_tests=yes]
)
AS_IF([test "x$enable_tests" = "xyes"], [
    PKG_CHECK_MODULES([CPPUNIT], [cppunit])
], [])
AC_SUBST([CPPUNIT_CFLAGS])
AC_SUBST([CPPUNIT_LIBS])
AM_CONDITIONAL([ENABLE_TESTS], [test "x$enable_tests" = "xyes"])

# =============
# Documentation
# =============
AC_ARG_WITH(docs,
	[AS_HELP_STRING([--without-docs], [Do not build documentation])],
	[with_docs="$withval"],
	[AS_IF([test "x$native_win32" = "xyes"], [with_docs=no], [with_docs=yes])]
)
AS_IF([test "x$with_docs" != "xno"], [
	AC_PATH_PROG(DOXYGEN, [doxygen])
	AS_IF([test -z "$DOXYGEN"], [
		AC_MSG_WARN([*** Could not find doxygen in your PATH.])
		AC_MSG_WARN([*** The documentation will not be built.])
		build_docs=no
	], [build_docs=yes])
], [build_docs=no])
AM_CONDITIONAL([WITH_LIBREVENGE_DOCS], [test "x$build_docs" != "xno"])

# =======
# Streams
# =======
AC_ARG_ENABLE(streams,
	[AS_HELP_STRING([--disable-streams], [Do not build librevenge-stream library])],
	[enable_streams="$enableval"],
	[enable_streams=yes]
)
AM_CONDITIONAL([BUILD_STREAMS], [test "x$enable_streams" != "xno"])

AS_IF([test "x$enable_streams" != "xno"], [
	# =========
	# Find zlib
	# =========
	PKG_CHECK_MODULES([ZLIB],[zlib],[],[
		AC_CHECK_HEADER(zlib.h, [ZLIB_CFLAGS=],
			[AC_MSG_ERROR(zlib.h not found. install zlib)], [])
		AC_CHECK_LIB(z, inflate, [ ZLIB_LIBS=-lz ],
			[AC_MSG_ERROR(zlib not found or functional)], [])
	])
	AC_SUBST(ZLIB_CFLAGS)
	AC_SUBST(ZLIB_LIBS)
])

# ==========
# Generators
# ==========
AC_ARG_ENABLE(generators,
	[AS_HELP_STRING([--disable-generators], [Do not build librevenge-generators library])],
	[enable_generators="$enableval"],
	[enable_generators=yes]
)
AM_CONDITIONAL([BUILD_GENERATORS], [test "x$enable_generators" != "xno"])

# ===================
# gdb pretty printers
# ===================
AC_ARG_ENABLE([pretty-printers],
	[AS_HELP_STRING([--enable-pretty-printers], [Install gdb pretty printers])],
	[enable_pretty_printers="$withval"],
	[enable_pretty_printers=no]
)
AC_ARG_WITH([gdb-data-dir],
	[AS_HELP_STRING([--with-gdb-data-dir], [Specify gdb data dir])],
	[gdb_data_dir="$withval"],
	[gdb_data_dir="/usr/share/gdb"]
)
AM_CONDITIONAL([BUILD_PRETTY_PRINTERS], [test "x$enable_pretty_printers" != "xno"])

AS_IF([test "x$enable_pretty_printers" != "xno"],
    [dnl
		GDBDATADIR="$gdb_data_dir"
		AC_PATH_PROG([GDB], [gdb])dnl
    ]
)
AC_SUBST([GDBDATADIR])

# =====================
# Prepare all .in files
# =====================
AC_CONFIG_FILES([
Makefile
data/Makefile
data/gdb/Makefile
data/gdb/auto-load/Makefile
data/gdb/auto-load/librevenge-$RVNG_MAJOR_VERSION.$RVNG_MINOR_VERSION.py:data/gdb/auto-load/librevenge.py.in
data/gdb/auto-load/librevenge-stream-$RVNG_MAJOR_VERSION.$RVNG_MINOR_VERSION.py:data/gdb/auto-load/librevenge-stream.py.in
src/Makefile
src/lib/Makefile
src/lib/librevenge.rc
src/lib/librevenge-generators.rc
src/lib/librevenge-stream.rc
src/test/Makefile
inc/Makefile
inc/librevenge/Makefile
inc/librevenge-generators/Makefile
inc/librevenge-stream/Makefile
docs/Makefile
docs/doxygen/Makefile
build/Makefile
build/win32/Makefile
librevenge-$RVNG_MAJOR_VERSION.$RVNG_MINOR_VERSION.pc:librevenge.pc.in
librevenge-generators-$RVNG_MAJOR_VERSION.$RVNG_MINOR_VERSION.pc:librevenge-generators.pc.in
librevenge-stream-$RVNG_MAJOR_VERSION.$RVNG_MINOR_VERSION.pc:librevenge-stream.pc.in
])
AC_OUTPUT

# ==============================================
# Display final informations about configuration
# ==============================================
AC_MSG_NOTICE([
==============================================================================
Build configuration:
	debug:           ${enable_debug}
	docs:            ${build_docs}
	tests:           ${enable_tests}
	generators:      ${enable_generators}
	streams:         ${enable_streams}
	werror:          ${enable_werror}
	pretty printers: ${enable_pretty_printers}
==============================================================================
])
