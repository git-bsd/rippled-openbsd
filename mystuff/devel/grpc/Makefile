# $OpenBSD: Makefile.template,v 1.90 2021/02/25 21:59:27 sthen Exp $
CATEGORIES =   mystuff/devel devel
HOMEPAGE =  https://grpc.io
MAINTAINER =        Bram Janssen <admin@7ce.nl>
PERMIT_PACKAGE =    Yes
COMMENT =   grpc libraries
DISTNAME =  grpc

ONLY_FOR_ARCHS =    amd64
VERSION =   1.25.0
GH_ACCOUNT =    grpc
GH_PROJECT =    grpc
GH_TAGNAME =    v${VERSION}

PKGNAME = ${DISTNAME}-${VERSION}

BUILD_DEPENDS =     devel/git


LIB_DEPENDS =       devel/protobuf 

RUN_DEPENDS =       net/libcares

#COMPILER=   base-clang ports-gcc
COMPILER=   base-clang

WANTLIB += ${COMPILER_LIBCXX} c m
WANTLIB += protobuf protoc

MODULES =       devel/cmake

CONFIGURE_STYLE =   cmake
CONFIGURE_ARGS =    -DCMAKE_BUILD_TYPE=Release \
            -DgRPC_INSTALL=ON \
            -DBUILD_SHARED_LIBS=OFF \
            -DgRPC_BUILD_TESTS=OFF \
            -DgRPC_SSL_PROVIDER=package \
            -DgRPC_PROTOBUF_PROVIDER=package \
            -DgRPC_ZLIB_PROVIDER=package \
            ../..
#            -DCMAKE_CXX_FLAGS='-Wno-error -Wno-error=unused-but-set-variable'\


WRKBUILD =  ${WRKSRC}/cmake/build
MAKE_JOBS = 4

#CXX_FLAGS = -Wno-error=unused-but-set-variable

pre-configure:
	rm -rf -- ${WRKSRC}; mkdir -p ${WRKSRC};\
	cd ${WRKSRC}; git clone -j ${MAKE_JOBS} -b ${GH_TAGNAME} https://github.com/${GH_ACCOUNT}/${GH_PROJECT} . ;\
	git submodule update --init -j ${MAKE_JOBS} ;\
	sed -i "s/LIBS = dl rt m pthread/LIBS = m pthread/g" Makefile ;\
	sed -i "s/#define HOST_NAME_MAX 64/#define HOST_NAME_MAX 255/g" \
	    third_party/benchmark/src/sysinfo.cc ;\
	sed -i "s/for (int i = 0; i < size /for (unsigned long int i = 0; i < size /g" \
	    third_party/boringssl/third_party/googletest/src/gtest-port.cc ;\
	sed -i "s/struct kinfo_proc info\[mib\[5\]\]//g" \
	    third_party/boringssl/third_party/googletest/src/gtest-port.cc 

#	sed -i 's/set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${_gRPC_C_CXX_FLAGS}")/set(CMAKE_CXX_FLAGS "${_gRPC_C_CXX_FLAGS} ${CMAKE_CXX_FLAGS}")/g' \
#	    ${WRKSRC}/CMakeLists.txt


do-configure:
	mkdir -p ${WRKBUILD}
	cd ${WRKBUILD} ; cmake ${CONFIGURE_ARGS}
	find ${WRKSRC}/cmake/build/third_party/benchmark -name flags.make -exec sed -i "s/\-Werror//g" {} \;
	find ${WRKSRC}/cmake/build/third_party/benchmark -name link.txt -exec sed -i "s/\-Werror//g" {} \;
    
post-configure:
	cd ${WRKSRC}/cmake; find . -type f -name link.txt -print0 | xargs -0 sed -i "s/\-lrt//g"

do-build:
	cd ${WRKBUILD} ; make -j ${MAKE_JOBS} 
#	-Wno-error=unused-but-set-variable

do-install:
	cd ${WRKBUILD}; make install -j ${MAKE_JOBS}


post-install:
	rm -rf ${PREFIX}/lib/cmake/grpc/
	rm -rf ${PREFIX}/lib/cmake/c-ares
	rm -rf ${PREFIX}/include/*ares*.h
	rm -rf ${PREFIX}/lib/libcares.a
	rm -rf ${PREFIX}/lib/pkgconfig/libcares.pc

	cp ${WRKBUILD}/lib*.a ${PREFIX}/lib
	cp ${WRKBUILD}/grpc_cpp_plugin ${PREFIX}/bin


.include <bsd.port.mk>
