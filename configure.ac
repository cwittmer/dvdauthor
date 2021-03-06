AC_INIT(DVDAuthor,0.7.0+,dvdauthor-users@lists.sourceforge.net)

AM_CONFIG_HEADER(src/config.h)
AC_CONFIG_AUX_DIR(autotools)

AM_INIT_AUTOMAKE

AC_PROG_INSTALL

AC_SYS_LARGEFILE

AC_HEADER_STDBOOL

have_dvdread=false
AC_CHECK_LIB(dvdread, DVDOpenFile, have_dvdread=true)
AM_CONDITIONAL(HAVE_DVDREAD, $have_dvdread)

AC_CHECK_LIB(gnugetopt, getopt_long)

dnl AC_CHECK_HEADERS initializes CPP, so must appear outside of any conditionals
AC_CHECK_HEADERS( \
    getopt.h \
    io.h \
)

AC_CHECK_FUNCS( \
    strndup \
    getopt_long \
    setmode \
)

usemagick=0

AC_CHECK_PROGS(MAGICKCONFIG, [Magick-config])
if test -n "$MAGICKCONFIG"; then
    ac_save_CPPFLAGS="$CPPFLAGS"
    ac_save_LIBS="$LIBS"
    MAGICK_CPPFLAGS="`$MAGICKCONFIG --cppflags`"
    MAGICK_LIBS="`$MAGICKCONFIG --ldflags` `$MAGICKCONFIG --libs`"
    CPPFLAGS="$CPPFLAGS $MAGICK_CPPFLAGS"
    LIBS="$MAGICK_LIBS $LIBS"
    AC_CHECK_FUNC(ExportImagePixels, usemagick=1, AC_MSG_NOTICE([ImageMagick does not support the function
         ExportImagePixels.  Please upgrade to ImageMagick 5.5.7 or newer]))
    CPPFLAGS="$ac_save_CPPFLAGS"
    LIBS="$ac_save_LIBS"
    if test "$usemagick" = 1; then
        AC_DEFINE(HAVE_MAGICK, 1, [Whether the ImageMagick libraries are available])
    fi
fi

if test "$usemagick" != 1; then
    AC_CHECK_PROGS(GMAGICKCONFIG, [GraphicsMagick-config])
    if test -n "$GMAGICKCONFIG"; then
        ac_save_CPPFLAGS="$CPPFLAGS"
        ac_save_LIBS="$LIBS"
        MAGICK_CPPFLAGS="`$GMAGICKCONFIG --cppflags`"
        MAGICK_LIBS="`$GMAGICKCONFIG --ldflags` `$GMAGICKCONFIG --libs`"
        CPPFLAGS="$CPPFLAGS $MAGICK_CPPFLAGS"
        LIBS="$MAGICK_LIBS $LIBS"
        AC_CHECK_FUNC(DispatchImage, usemagick=1)
        CPPFLAGS="$ac_save_CPPFLAGS"
        LIBS="$ac_save_LIBS"
        if test "$usemagick" = 1; then
            AC_DEFINE(HAVE_GMAGICK, 1, [Whether the GraphicsMagick libraries are available])
        fi
    fi
fi

if test "$usemagick" != 1; then
    AC_CHECK_LIB(z, zlibVersion, test, AC_MSG_ERROR([You must have libz(-devel) installed]))

    AC_CHECK_LIB(png, png_sig_cmp, test, AC_MSG_ERROR([You must have libpng(-devel) installed]), -lz -lm)
    MAGICK_CPPFLAGS=
    MAGICK_LIBS="-lpng -lz -lm"
fi

AC_SUBST(MAGICK_CPPFLAGS)
AC_SUBST(MAGICK_LIBS)

PKG_CHECK_MODULES([FONTCONFIG], [fontconfig], [AC_DEFINE(HAVE_FONTCONFIG, 1, [whether Fontconfig is available])], [:])
AC_SUBST(FONTCONFIG_CFLAGS)
AC_SUBST(FONTCONFIG_LIBS)

PKG_CHECK_MODULES([FRIBIDI], [fribidi], [AC_DEFINE(HAVE_FRIBIDI, 1, [whether FriBidi is available])], [:])
AC_CHECK_TYPE(FriBidiParType, [AC_DEFINE(HAVE_FRIBIDI2, 1, [Whether Fribidi is version 2])], [], [#include <fribidi/fribidi-bidi-types.h>])
AC_SUBST(FRIBIDI_CFLAGS)
AC_SUBST(FRIBIDI_LIBS)

AC_CHECK_PROGS(FREETYPECONFIG, [freetype-config])
if test -n "$FREETYPECONFIG"; then
    FREETYPE_CPPFLAGS="`$FREETYPECONFIG --cflags`"
    FREETYPE_LIBS="`$FREETYPECONFIG --libs`"
    AC_DEFINE(HAVE_FREETYPE, 1, [Whether FreeType is available])

    ac_save_CPPFLAGS="$CPPFLAGS"
    CPPFLAGS="$CPPFLAGS $FREETYPE_CPPFLAGS"
    AC_CHECK_HEADERS(ft2build.h)
    CPPFLAGS="$ac_save_CPPFLAGS"
fi

AC_SUBST(FREETYPE_CPPFLAGS)
AC_SUBST(FREETYPE_LIBS)

AC_ARG_ENABLE([default-video-format],
AS_HELP_STRING([--enable-default-video-format=format], [specify a default video format, either NTSC or PAL, to be used if no configuration or input setting is given]),
[
if test "$enable_default_video_format" == "PAL" -o "$enable_default_video_format" == "pal"; then
    AC_DEFINE(DEFAULT_VIDEO_FORMAT, 2, [Default video format, 1 => NTSC, 2 => PAL])
elif test "$enable_default_video_format" == "NTSC" -o "$enable_default_video_format" == "ntsc"; then
    AC_DEFINE(DEFAULT_VIDEO_FORMAT, 1, [Default video format, 1 => NTSC, 2 => PAL])
else
    AC_MSG_ERROR([specify either --enable-default-video-format=pal or --enable-default-video-format=ntsc])
fi
])

AC_ARG_ENABLE([localize-filenames],
AS_HELP_STRING([--enable-localize-filenames], [specifies that filenames are to be interpreted in the locale encoding. If omitted, they are always interpreted as UTF-8]),
[
AC_DEFINE(LOCALIZE_FILENAMES, 1, [Whether to localize filenames])
])

dnl AM_LANGINFO_CODESET

AM_ICONV

AM_PATH_XML2(2.6.0, , AC_MSG_ERROR([You must have libxml2 >= 2.6.0 installed]))

AC_CHECK_DECLS(O_BINARY, , , [ #include <fcntl.h> ] )

AC_OUTPUT(Makefile doc/Makefile src/Makefile)
