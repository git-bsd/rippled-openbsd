# $OpenBSD: Makefile.template,v 1.90 2021/02/25 21:59:27 sthen Exp $

COMMENT-reporting =     XRP ledger reporting node
COMMENT = 	XRP ledger regular node

VERSION =       1.9.0
DISTNAME =      rippled-${VERSION}
PKGNAME =       ${DISTNAME}

CATEGORIES =    net

HOMEPAGE =      https://xrpl.org

MAINTAINER =    Bram Janssen <admin@7ce.nl>

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
                        

LIB_DEPENDS =   archivers/bzip2 \
                archivers/libarchive \
                archivers/lz4 \
                archivers/zstd \
                converters/libiconv \
                security/libb2 

SEPARATE_BUILD = YES

MODULES =       devel/cmake

MODCMAKE_VERBOSE =      yes
MODCMAKE_COLOR = yes
CONFIGURE_STYLE =       cmake

FLAVORS =   main reporting

FLAVOR ?=

# turn unity and ninja off if build errors and unable to clearly see what caused it
CONFIGURE_ARGS =	-DCMAKE_BUILD_TYPE=Release -Dstatic=true -Dunity=ON -Dtests=OFF \
                    -Dvalidator_keys=ON


USE_NINJA = YES

.if ${FLAVOR:L:Mreporting}

BUILD_DEPENDS +=     databases/postgresql \
                     devel/libuv \
                     devel/bison \
                     mystuff/devel/cpp-driver-cassandra

MAKE_ENV =      AUTOCONF_VERSION=2.71 

# tried LDFLAGS to include cass and uv, did not work
# this is suboptimal as now it is included in every build step
CONFIGURE_ARGS +=       -Dreporting=ON \
                        -Dcassandra=/usr/local/lib \
                        -Dcassandra_includes:PATH=/usr/local/include \
                        -DCMAKE_CXX_FLAGS_DEBUG="-lcassandra_static -luv -lstdc++"

#USE_NINJA = YES
.endif


MAKE_JOBS = 2


post-configure:
	find ${WRKBUILD} -type f \
	    \( -name build.ninja -o -name link.txt \)\
	    -exec sed -i -e "s/\-lrt//g" -e "s/\-ldl//g" {} \;
	find  ${WRKSRC}/.nih_c/ -type f -name posix_file.ipp -exec sed -i '/#ifndef __APPLE__/,/#endif/d' {} \;



post-build:
	cd ${WRKBUILD}; cmake --build . --target validator-keys

#	mkdir -p ${WRKBUILD}/validator-keys/build/gcc.test ; cd ${WRKBUILD}/validator-keys/build/gcc.test ; \
#	cmake -DCMAKE_BUILD_TYPE=Release ../.. ;\

#	cd ${WRKBUILD}/validator-keys/build ;\
	
	

post-install:
	rm -rf ${PREFIX}/etc
	${INSTALL_SCRIPT} ${WRKBUILD}/validator-keys/validator-keys \
	    ${PREFIX}/bin
	    #	cd ${WRKBUILD}; cmake --build . --target validator-keys/install

	${INSTALL_DATA_DIR}  ${PREFIX}/share/examples/rippled
	${INSTALL_DATA_DIR}  ${WRKINST}/${SYSCONFDIR}/rippled
	sed -i 's/\/var\/lib/\/var\/run/g' ${WRKSRC}/cfg/*

.if !${FLAVOR:L:Mreporting}
	${INSTALL_DATA}  ${WRKSRC}/cfg/rippled-example.cfg \
	    ${PREFIX}/share/examples/rippled/rippled-example.cfg
.endif

.if ${FLAVOR:L:Mreporting}
	${INSTALL_DATA}  ${WRKSRC}/cfg/rippled-reporting.cfg \
	    ${PREFIX}/share/examples/rippled/rippled-reporting.cfg
.endif

	${INSTALL_DATA}  ${WRKSRC}/cfg/validators-example.txt \
	    ${PREFIX}/share/examples/rippled/validators.txt

	${INSTALL_DATA_DIR}  ${PREFIX}/share/doc/rippled
	${INSTALL_DATA}  ${WRKSRC}/LICENSE.md ${PREFIX}/share/doc/rippled



.include <bsd.port.mk>
