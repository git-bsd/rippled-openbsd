# $OpenBSD: Makefile.template,v 1.90 2021/02/25 21:59:27 sthen Exp $

COMMENT = soci database driver

DISTNAME =	soci
PKGNAME =  ${DISTNAME}-0.0.0

# this will break the distinfo.
GH_ACCOUNT =	soci
GH_PROJECT =	soci
#GH_COMMIT =	master
GH_TAGNAME = 04e1870294918d20761736743bb6136314c42dd5
CATEGORIES =	mystuff/devel devel
HOMEPAGE =	https://soci.sourceforge.net
MAINTAINER =    Bram Janssen <admin@7ce.nl>

SHARED_LIBS +=  soci_core                 0.0 # 0.0                                                                    │
SHARED_LIBS +=  soci_empty                0.0 # 0.0                                                                    │
SHARED_LIBS +=  soci_sqlite3              0.0 # 0.0
# need to check 
PERMIT_PACKAGE = no

#WANTLIB =		???
COMPILER =		base-clang

MODULES =		devel/cmake

LIB_DEPENDS =		devel/boost \
                    devel/boost,-md
#                    security/heimdal

CONFIGURE_STYLE =	cmake
CONFIGURE_ARGS =    -DWITH_BOOST=ON -DBoost_FOUND=ON \
                    -DBoost_NO_BOOST_CMAKE=ON -DBoost_DATE_TIME_FOUND=ON \
                    -DSOCI_HAVE_BOOST=ON -DSOCI_HAVE_BOOST_DATE_TIME=ON \
                    -DSOCI_DB2=OFF -DSOCI_FIREBIRD=OFF -DSOCI_MYSQL=OFF -DSOCI_ODBC=OFF \
                    -DSOCI_ORACLE=OFF -DSOCI_POSTGRESQL=OFF -DSOCI_SQLITE3=ON \
                    -DCMAKE_BUILD_TYPE=Release \
                    -DSOCI_CXX_C11=ON -DSOCI_STATIC=ON -DSOCI_LIBDIR=lib -DSOCI_SHARED=OFF -DSOCI_TESTS=OFF

.include <bsd.port.mk>
