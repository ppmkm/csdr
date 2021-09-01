AC_INIT([csdr], 0.18.0)
AM_INIT_AUTOMAKE([foreign])
AC_CONFIG_MACRO_DIRS([m4])
AC_PROG_CC
AC_PROG_CXX
AC_PROG_LIBTOOL
PKG_PROG_PKG_CONFIG
AC_ENABLE_SHARED
AC_DISABLE_STATIC
LT_INIT
PKG_INSTALLDIR
AC_CONFIG_FILES(Makefile)
AC_CONFIG_FILES([csdr.pc:csdr.pc.in])
AC_CHECK_LIB([m],[floorf])
PKG_CHECK_MODULES([FFTW3], [fftw3f])
AX_PTHREAD
AX_GCC_FUNC_ATTRIBUTE([ifunc])
AC_ARG_ENABLE([ima_adpcm],
    AS_HELP_STRING([[[--disable-ima-adpcm]]], [Disable compilation of IMA ADPCM codec extensions]),
    [case $enableval in
        no|off)
            ;;
        *)
            AC_DEFINE(USE_IMA_ADPCM)
            ;;
    esac], [
        AC_DEFINE(USE_IMA_ADPCM)
    ])
AC_DEFINE(USE_FFTW)
AC_ARG_ENABLE([gpl],
    AS_HELP_STRING([[[--disable-gpl]]], [Disable compilation of extensions licensed under the GPL]),
    [case $enableval in
        no|off)
            enable_gpl=no
            ;;
        *)
            enable_gpl=yes
            AC_DEFINE(LIBCSDR_GPL)
        ;;
    esac], [
        enable_gpl=yes
        AC_DEFINE(LIBCSDR_GPL)
    ])
AM_CONDITIONAL([GPL], [test x$enable_gpl = xyes])
AC_ARG_ENABLE([neon],
    AS_HELP_STRING([[[--enable-neon]]], [Enable ARM CPU NEON optimizations]),
    [case $enableval in
        no|off)
            enable_neon=no
            ;;
        *)
            enable_neon=yes
            ;;
    esac], [case `uname -m` in
        arm*)
            if grep "neon" /proc/cpuinfo > /dev/null ; then
                enable_neon=yes
            else
                enable_neon=no
            fi
            ;;
        aarch64*)
            # aarch64 mandated neon
            enable_neon=yes
            ;;
        *)
            enable_neon=no
            ;;
    esac])
if test yes = $enable_neon; then
    AC_DEFINE([NEON_OPTS])
    case `uname -m` in
        arm*)
            PARAMS_SIMD="-mfloat-abi=hard -mfpu=neon -mvectorize-with-neon-quad"
            ;;
        *)
            PARAMS_SIMD=""
            ;;
    esac
fi
OPTIMIZED_FLAGS="-g -Ofast -march=native -ffast-math"
case `uname -m` in
    arm*)
        PARAMS_ARCH="-march=armv7-a -mtune=cortex-a8 -funsafe-math-optimizations -Wformat=0"
        CFLAGS="${OPTIMIZED_FLAGS}"
        CXXFLAGS="${OPTIMIZED_FLAGS}"
        ;;
    aarch64*)
        PARAMS_ARCH="-march=armv8-a -mtune=cortex-a72 -funsafe-math-optimizations -Wformat=0"
        CFLAGS="${OPTIMIZED_FLAGS}"
        CXXFLAGS="${OPTIMIZED_FLAGS}"
        ;;
    x86_64)
        PARAMS_ARCH="-mfpmath=sse"
        AS_IF([test yes = AS_VAR_GET([ax_cv_have_func_attribute_ifunc])],
            [CFLAGS="${OPTIMIZED_FLAGS}"
            CXXFLAGS="${OPTIMIZED_FLAGS}"])
        ;;
esac
CFLAGS="${CFLAGS} ${PARAMS_ARCH} ${PARAMS_SIMD}"
CXXFLAGS="${CXXFLAGS} ${PARAMS_ARCH} ${PARAMS_SIMD}"
AC_OUTPUT