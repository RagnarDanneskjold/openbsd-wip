# $OpenBSD$

COMMENT =	Algol 60 interpreter
DISTNAME =	a60-0.23a
CATEGORIES =	lang

HOMEPAGE =	https://www.bertnase.de/a60/
MAINTAINER =	Brian Callahan <bcallah@openbsd.org>

# GPLv2+
PERMIT_PACKAGE_CDROM =	Yes

WANTLIB += ICE SM X11 Xaw Xext Xmu Xt c m

MASTER_SITES =	https://www.bertnase.de/a60/

CONFIGURE_STYLE =	gnu

MAKE_FLAGS =	FLAGS="${CFLAGS} -Dunix" \
		LIBDIR="${PREFIX}/share/a60" \
		XINCLUDE="-I${X11BASE}/include" \
		XLIB="-L${X11BASE}/lib -lXaw -lXt -lSM -lICE -lXext -lXmu -lX11"

FAKE_FLAGS =	prefix="${DESTDIR}${PREFIX}" \
		LIBDIR="${DESTDIR}${PREFIX}/share/a60"

post-install:
	${INSTALL_DATA} ${WRKSRC}/a60.info ${PREFIX}/info
	${INSTALL_DATA_DIR} ${PREFIX}/share/doc/a60
	${INSTALL_DATA} ${WRKSRC}/RRA60.ps ${PREFIX}/share/doc/a60
	${INSTALL_DATA_DIR} ${PREFIX}/share/examples/a60
	${INSTALL_DATA} ${WRKSRC}/examples/*.a60 ${PREFIX}/share/examples/a60

.include <bsd.port.mk>
