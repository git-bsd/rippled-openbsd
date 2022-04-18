# $OpenBSD: Makefile.template,v 1.90 2021/02/25 21:59:27 sthen Exp $

COMMENT = cpp driver for cassandra db

DISTNAME =	cpp-driver-cassandra
PKGNAME =  cpp-driver-cassandra-0.0.0

# this will break the distinfo.
GH_ACCOUNT =	datastax
GH_PROJECT =	cpp-driver
GH_COMMIT =	master

CATEGORIES =	mystuff/devel devel
HOMEPAGE =	https://datastax.com

MAINTAINER =    Bram Janssen <admin@7ce.nl>

# Apache-2.0 License 
PERMIT_PACKAGE =	Yes

#WANTLIB =		???
COMPILER =		base-clang

MODULES =		devel/cmake

LIB_DEPENDS =		devel/libuv \
                    security/heimdal

CONFIGURE_STYLE =	cmake
CONFIGURE_ARGS =	-DCASS_BUILD_STATIC=ON -DCASS_BUILD_SHARED=ON \
                    -DCMAKE_BUILD_TYPE=Debug \
                    -DCASS_USE_STATIC_LIBS=OFF 

.include <bsd.port.mk>
