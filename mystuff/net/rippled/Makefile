# $OpenBSD: Makefile.template,v 1.90 2021/02/25 21:59:27 sthen Exp $

COMMENT-reporting =     XRP ledger reporting node
COMMENT-main = 	XRP ledger regular node

VERSION =       1.9.0
DISTNAME =      rippled-${VERSION}
CATEGORIES =    net
HOMEPAGE =      https://xrpl.org
MAINTAINER =    Bram Janssen <admin@7ce.nl>



PKGNAME =               ${DISTNAME}
PKGNAME-main =          ${DISTNAME}
PKGNAME-reporting =     rippled-reporting-${VERSION}

#MULTI_PACKAGES =     -main -reporting

# ISC License
PERMIT_PACKAGE =        Yes

GH_ACCOUNT =            ripple
GH_PROJECT =            rippled
GH_TAGNAME =            ${VERSION}


SHARED_LIBS +=  secp256k1                 0.0 

WANTLIB +=       ${COMPILER_LIBCXX} bz2 c crypto z pthread expat m
WANTLIB +=      archive b2 charset iconv lz4 lzma zstd

DIST_SUBDIR =           rippled

COMPILER =              base-clang
COMPILER_LANGS =        c++

BUILD_DEPENDS +=        devel/cmake \
                        mystuff/devel/boost \
                        mystuff/devel/boost,-md \
                        mystuff/devel/grpc \
                        devel/protobuf 
#                        \
#                        mystuff/devel/soci

LIB_DEPENDS =           archivers/bzip2 \
                        archivers/libarchive \
                        archivers/lz4 \
                        archivers/zstd \
                        converters/libiconv \
                        security/libb2 
SEPARATE_BUILD = YES
MODULES =       devel/cmake

FLAVORS =   main reporting
FLAVOR ?= main


MODCMAKE_VERBOSE =      yes
MODCMAKE_COLOR =        yes
CONFIGURE_STYLE =       cmake
USE_NINJA =             YES
MAKE_JOBS =             2


CONFIGURE_ARGS =	-DCMAKE_BUILD_TYPE=Debug -Dstatic=true -Dunity=ON -Dtests=OFF
#\
#                    -Dsoci=/usr/local/lib/libsoci_core.a
#                    -DCMAKE_CXX_FLAGS='-Wno-error -Wno-error=deprecated-copy '


.if ${FLAVOR:Mmain}
CONFIGURE_ARGS += -Dvalidator_keys=ON
.else
#.if ${MULTI_PACKAGES:M-reporting}
BUILD_DEPENDS +=     databases/postgresql \
                     devel/libuv \
                     devel/bison \
                     mystuff/devel/cpp-driver-cassandra
AUTOCONF_VERSION = 2.71 

CONFIGURE_ARGS +=       -Dreporting=ON \
                        -Dcassandra=/usr/local/lib \
                        -Dcassandra_includes:PATH=/usr/local/include \
                        -DCMAKE_CXX_FLAGS_DEBUG="-lcassandra_static -luv -lstdc++"
#USE_NINJA = YES
.endif



post-configure:
	find ${WRKBUILD} -type f \
	    \( -name build.ninja -o -name link.txt \)\
	    -exec sed -i -e "s/\-lrt//g" -e "s/\-ldl//g" {} \;

#    find ${WRKSRC}/.nih_c
	find  ${WRKSRC}/.nih_c/ -type f -name posix_file.ipp -exec sed -i '/#ifndef __APPLE__/,/#endif/d' {} \;
# this could become a patch?
	sed -i 's/format\-truncation/format\-truncation \-Wno\-error=deprecated-copy \-Wno-error/g' ${WRKSRC}/Builds/CMake/deps/Soci.cmake
#	sed -i "s/\$\<\$\<BOOL:${werr}>:\-Werror>//g" ${WRKSRC}/Builds/CMake/RippledCompiler.cmake


.if ${FLAVOR:Mmain}
post-build:
	cd ${WRKBUILD}; cmake --build . --target validator-keys 
.endif

post-install:
	rm -rf ${PREFIX}/etc
	${INSTALL_DATA_DIR}  ${PREFIX}/share/examples/rippled
	${INSTALL_DATA_DIR}  ${WRKINST}/${SYSCONFDIR}/rippled
	sed -i 's/\/var\/lib/\/var\/run/g' ${WRKSRC}/cfg/*

.if ${FLAVOR:Mmain}
	${INSTALL_SCRIPT} ${WRKBUILD}/validator-keys/validator-keys \
	    ${PREFIX}/bin
	${INSTALL_DATA}  ${WRKSRC}/cfg/rippled-example.cfg \
	    ${PREFIX}/share/examples/rippled/rippled-example.cfg
.endif

.if ${FLAVOR:Mreporting}
	${INSTALL_DATA}  ${WRKSRC}/cfg/rippled-reporting.cfg \
	    ${PREFIX}/share/examples/rippled/rippled-reporting.cfg
	mv ${PREFIX}/bin/rippled-reporting ${PREFIX}/bin/rippled
.endif

	${INSTALL_DATA}  ${WRKSRC}/cfg/validators-example.txt \
	    ${PREFIX}/share/examples/rippled/validators.txt

	${INSTALL_DATA_DIR}  ${PREFIX}/share/doc/rippled
	${INSTALL_DATA}  ${WRKSRC}/LICENSE.md ${PREFIX}/share/doc/rippled



.include <bsd.port.mk>
