# $OpenBSD: Makefile,v 1.204 2017/05/12 15:11:18 espie Exp $

COMMENT-main=	MS Exchange groupware suite replacement
COMMENT-mapi=	kopano MAPI extensions for PHP

PKGNAME=	kopano-core-${V}
PKGNAME-main=	kopano-core-${V}
PKGNAME-mapi=	kopano-mapi-${V}

CATEGORIES=	mail www productivity

EXTRACT_SUFX =	.tar.xz

SHARED_LIBS +=  mapi			  3.0 # .0.0
SHARED_LIBS +=  kcarchiver                0.0 # 0.0
SHARED_LIBS +=  kcarchivercore            0.0 # 0.0
SHARED_LIBS +=  kcfreebusy                0.0 # 0.0
SHARED_LIBS +=  kchl                      0.0 # 0.0
SHARED_LIBS +=  kcicalmapi                0.0 # 0.0
SHARED_LIBS +=  kcinetmapi                0.0 # 0.0
SHARED_LIBS +=  kcmapi                    0.0 # 0.0
SHARED_LIBS +=  kcpyconv                  0.0 # 0.0
SHARED_LIBS +=  kcpydirector              0.0 # 0.0
SHARED_LIBS +=  kcserver                  0.0 # 0.0
SHARED_LIBS +=  kcsoap                    0.0 # 0.0
SHARED_LIBS +=  kcssl                     0.0 # 0.0
SHARED_LIBS +=  kcsync                    0.0 # 0.0
SHARED_LIBS +=  kcutil                    0.0 # 0.0

MULTI_PACKAGES=	-main -mapi

MODULES=	devel/gettext \
		lang/python \
		lang/php

MODPHP_DO_SAMPLE=mapi
MESSAGE-main=	# empty
UNMESSAGE-main=	# empty

BUILD_DEPENDS=\
		devel/swig \
		textproc/xmlto \
		sysutils/coreutils

# libcurl is searched by configure but unused
BUILD_DEPENDS += net/curl

WANTLIB += ${MODGETTEXT_WANTLIB}
WANTLIB += m pthread z vmime execinfo icudata icui18n icuuc
WANTLIB += boost_filesystem-mt boost_system-mt lzma idn2 crypto

WANTLIB-main += ${WANTLIB} ${MODPY_WANTLIB}
WANTLIB-main += c ical icalss lber-2.4
WANTLIB-main += mysqlclient ncurses sasl2 ssl util xml2
WANTLIB-main += ldap_r-2.4 gsoapssl++
WANTLIB-main += ffi gmp gnutls hogweed nettle p11-kit ${LIBCXX} tasn1

RUN_DEPENDS-main=${MODGETTEXT_RUN_DEPENDS} \
		${MODPY_RUN_DEPENDS} \
		databases/xapian-bindings,-python \
		devel/libmagic \
		lang/python/2.7,-bsddb \
		textproc/catdoc
LIB_DEPENDS-main= ${MODGETTEXT_LIB_DEPENDS} \
		databases/mariadb \
		databases/openldap \
		devel/boost \
		devel/gsoap \
		devel/libexecinfo \
		devel/libvmime>=0.9.2p1 \
		textproc/icu4c \
		textproc/libical \
		textproc/libxml

WANTLIB-mapi += ${WANTLIB}
WANTLIB-mapi += ical icalss mapi
WANTLIB-mapi += xml2
WANTLIB-mapi += ffi gmp gnutls hogweed nettle p11-kit ${LIBCXX} tasn1

LIB_DEPENDS-mapi= ${BASE_PKGPATH}>=${V}
RUN_DEPENDS-mapi= ${MODPHP_RUN_DEPENDS} \
		 ${MODGETTEXT_RUN_DEPENDS}

USE_GMAKE=	Yes

USE_LIBTOOL=	gnu

MODPY_ADJ_FILES=tools/python-scripts/optimize-imap.py \
		tools/python-scripts/update-resource-recipients \
		installer/linux/kopano7-upgrade \
		ECtools/utils/kopano-mailbox-permissions \
		ECtools/utils/kopano-set-oof

CONFIGURE_STYLE=	autoconf
AUTOCONF_VERSION=	2.69

CONFIGURE_ENV=	CPPFLAGS="-fpermissive -I${LOCALBASE}/include \
			-I${LOCALBASE}/include/kopano" \
		LDFLAGS="-L${LOCALBASE}/lib -lexecinfo \
			-L${LOCALBASE}/lib/kopano" \
		ZAFARA_LDFLAGS="-L${LOCALBASE}/lib/kopano" \
		PHP_SYSCONF_DIR="${SYSCONFDIR}/php-${MODPHP_VERSION}" \
		ac_cv_lib_pam_pam_start=no
CONFIGURE_ARGS=	--disable-static \
		--with-gsoap-prefix=${LOCALBASE} \
		--with-mapi-configdir=${SYSCONFDIR}/mapi \
		--with-userscript-prefix=${SYSCONFDIR}/kopano/userscripts \
		--with-quotatemplate-prefix=${SYSCONFDIR}/kopano/quotamail \
		--with-searchscripts-prefix=${SYSCONFDIR}/kopano/searchscripts \
		--with-mapi-timezonedir=${LOCALBASE}/share/kopano/timezones \
		--enable-release \
		--enable-oss \
		--enable-unicode \
		--enable-python \
		--enable-icu \
		--with-distro=openbsd \
		--with-vmime-prefix=${LOCALBASE}/include \
		--with-ical-prefix=${LOCALBASE}/include \
		--with-clucene-lib-prefix=${LOCALBASE}/lib \
		--with-clucene-include-prefix=${LOCALBASE}/include \
		--with-python=${MODPY_BIN} \
		--with-php-config=${MODPHP_PHP_CONFIG}

# force build with clucene >= 2.x
CONFIGURE_ENV +=ac_cv_lib_clucene__ZN6lucene5index11IndexReader4openEPKc=yes
MAKE_FLAGS +=	CLUCENE_CFLAGS="$$(pkg-config --cflags libclucene-core)" \
		CLUCENE_LIBS="$$(pkg-config --libs libclucene-core)"

FAKE_FLAGS=	sysconfdir=${PREFIX}/share/examples \
		USERSCRIPTDIR=${PREFIX}/share/examples/kopano/userscripts \
		QUOTATEMPLATEDIR=${PREFIX}/share/examples/kopano/quotamail \
		SEARCHSCRIPTSDIR=${PREFIX}/share/examples/kopano/searchscripts \
		MAPICONFIGDIR=${PREFIX}/share/examples/kopano/mapi

SUB_SCRIPTS=	createcompany.d/00createpublic groups_common.sh \
		createuser.d/00createstore companies_common.sh \
		users_common.sh

post-patch:
	ln -sf ${MODPY_BIN} ${WRKDIR}/bin/python
	ln -sf ${LOCALBASE}/bin/gcp ${WRKDIR}/bin/cp
	ln -sf ${LOCALBASE}/bin/gmv ${WRKDIR}/bin/mv

pre-configure:
	cd ${WRKSRC}/installer/linux && \
		for i in *.cfg ldap.m4 createuser.dotforward ; do \
			${SUBST_CMD} $$i; \
			perl -pi -e 's/\r\n/\n/;' $$i; done
	for i in ${SUB_SCRIPTS}; do \
		${SUBST_CMD} ${WRKSRC}/installer/userscripts/$$i; done
	#${SUBST_CMD} ${WRKSRC}/php-ext/Makefile.in
	${SUBST_CMD} ${WRKSRC}/spooler/{DAgent,Spooler}.cpp \
		${WRKSRC}/tools/python-scripts/kopano-cachestat
	perl -pi -e 's,/usr/share/kopano,${PREFIX}/share/examples/kopano,g;' \
		-e 's,/etc/kopano,${SYSCONFDIR}/kopano,g;' \
		-e 's,/usr/bin,${PREFIX}/bin,g;' \
		-e 's,/var/lib/kopano,/var/db/kopano,g;' \
		-e 's,ssl-certificate.sh,ssl-certificates.sh,g' \
		${WRKSRC}/doc/manual.xml
	perl -pi -e 's,/bin/bash,/bin/sh,g' \
		${WRKSRC}/installer/searchscripts/{attachments_parser,zmktemp}

post-install:
	${INSTALL_DATA_DIR} ${WRKINST}${INSTDIR}
	${INSTALL_DATA_DIR} ${PREFIX}/share/examples/php-${MODPHP_VERSION}

	${SUBST_CMD} -m 0644 -c ${FILESDIR}/kopano.m4 ${PREFIX}/share/examples/kopano/kopano.m4
	${SUBST_CMD} -m 0644 -c ${FILESDIR}/local_kopano.m4 ${PREFIX}/share/examples/kopano/local_kopano.m4

	# move files where we want them to be
	mv ${PREFIX}/share/doc/kopano-gateway/* \
		${PREFIX}/share/doc/kopano
	mv ${PREFIX}/share/doc/kopano/example-config/* \
		${PREFIX}/share/examples/kopano
	mv ${PREFIX}/share/doc/kopano/kopano.schema \
		${PREFIX}/share/examples/kopano
#	mv ${PREFIX}/share/examples/kopano.ini \
#		${PREFIX}/share/examples/kopano
#	mv ${PREFIX}/share/examples/kopano/kopano.ini \
#		${PREFIX}/share/examples/php-${MODPHP_VERSION}/${MODPHP_DO_SAMPLE}.ini
	rm ${PREFIX}/share/examples/cron.daily/kopano-client-update
	rm ${PREFIX}/share/examples/kopano/unix.cfg
	rm ${PREFIX}/share/doc/kopano/ssl-certificates.sh
	rmdir ${PREFIX}/share/doc/{kopano/example-config,kopano-gateway}
	rm -rf ${PREFIX}/share/examples/{cron.daily,init.d,logrotate.d,sysconfig,kopano/kopano-gateway,kopano/license}
	perl -pi -e 's,/usr/bin/env python,${MODPY_BIN},g' \
		${PREFIX}/sbin/kopano-search \
		${PREFIX}/sbin/kopano-search-xapian-compact.py \
		${PREFIX}/lib/python${MODPY_VERSION}/site-packages/kopano_search/__init__.py \
		${PREFIX}/lib/python${MODPY_VERSION}/site-packages/kopano_search/plaintext.py \
		${PREFIX}/lib/python${MODPY_VERSION}/site-packages/kopano/daemon/version/version_info.py

	${MODPY_BIN} ${MODPY_LIBDIR}/compileall.py \
		${PREFIX}/share/kopano-dagent/python \
		${PREFIX}/share/kopano-spooler/python \
		${PREFIX}/lib/python${MODPY_VERSION}/site-packages

	chown -R ${SHAREOWN}:${SHAREGRP} ${WRKINST}${INSTDIR}
	chown -R ${SHAREOWN}:${SHAREGRP} ${PREFIX}/share/doc/kopano
	chown ${BINOWN}:${BINGRP} ${PREFIX}/share/examples/kopano/kopano.m4 \
		${PREFIX}/share/examples/kopano/local_kopano.m4
	chmod ${SHAREMODE} ${PREFIX}/share/examples/kopano/kopano.m4 \
		${PREFIX}/share/examples/kopano/local_kopano.m4

.include <bsd.port.mk>
