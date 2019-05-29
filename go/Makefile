# Created by: Devon H. O'Dell <devon.odell@gmail.com>
# $FreeBSD: head/lang/go/Makefile 500961 2019-05-07 14:02:40Z jlaffaye $

PORTNAME=	go
PORTVERSION=	1.12.5
PORTEPOCH=	1
CATEGORIES=	lang
MASTER_SITES=	https://golang.org/dl/
DISTNAME=	go${PORTVERSION}.src

PATCH_SITES=	https://github.com/golang/go/commit/
# https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=221297#c23
PATCHFILES=	3b6216ed0601c81fe42c2a4738d419afccb62163.patch:-p1 \
		0bd101cecc5458a8463b8d672bf1745c3cbb7c02.patch:-p1 \
		a6e892d26d6ee59799f55bc50521605ebf75ea67.patch:-p1

MAINTAINER=	jlaffaye@FreeBSD.org
COMMENT=	Go programming language

LICENSE=	BSD3CLAUSE

USES=		shebangfix
SHEBANG_LANG=	sh perl
SHEBANG_FILES=	misc/benchcmp \
		misc/nacl/go_nacl_*_exec \
		misc/wasm/go_js_wasm_exec \
		src/net/http/cgi/testdata/test.cgi
SHEBANG_GLOB=	*.bash *.pl *.sh

sh_OLD_CMD=	/bin/bash "/usr/bin/env bash"
sh_CMD=		${SH}

WRKSRC=		${WRKDIR}/go
ONLY_FOR_ARCHS=	i386 amd64 armv6 armv7 powerpc64

OPTIONS_DEFINE_i386=	GO387
GO387_DESC=	Do not generate code with SSE2 (for old x86 CPU)

.include <bsd.port.pre.mk>

GOARCH=${ARCH:S/aarch64/arm64/:C/armv.*/arm/:S/i386/386/:S/powerpc64/ppc64/}

.if ${ARCH} == armv6 || ${ARCH} == armv7
GOARM=${ARCH:C/armv//}
.endif

.if ${ARCH} == powerpc64
BUILD_DEPENDS=	go112>=1.12:lang/go112
GO_BOOTSTRAP=	${LOCALBASE}/go112
USE_GCC=	yes
.else
BUILD_DEPENDS=	go14>=1.4:lang/go14
GO_BOOTSTRAP=	${LOCALBASE}/go14
.endif

.ifdef QEMU_EMULATING
IGNORE=		fails to build with qemu-user-static
.endif

.if ${PORT_OPTIONS:MGO387}
GO386=387
.endif

PLIST_SUB+=	opsys_ARCH=${OPSYS:tl}_${GOARCH}

post-patch:
	@cd ${WRKSRC} && ${FIND} . -name '*.orig' -delete
	@${REINPLACE_CMD} -e 's|^if ulimit -T|false \&\& &|' ${WRKSRC}/src/run.bash

do-build:
	cd ${WRKSRC}/src && \
		GOROOT=${WRKSRC} GOROOT_FINAL=${PREFIX}/go \
		GOROOT_BOOTSTRAP=${GO_BOOTSTRAP} CC=${CC} \
		GOBIN= GOARCH=${GOARCH} GOOS=${OPSYS:tl} \
		GO386=${GO386} GOARM=${GOARM} CGO_ENABLED=1 \
		GOCACHE=${TMPDIR:U/tmp} \
		${SH} make.bash -v
	${RM} -r ${WRKSRC}/pkg/obj \
	    ${WRKSRC}/pkg/bootstrap \
	    ${WRKSRC}/pkg/${OPSYS:tl}_${GOARCH}/cmd

do-install:
	@${CP} -a ${WRKSRC} ${STAGEDIR}${PREFIX}
.for f in go gofmt
	@${LN} -sf ../go/bin/${f} ${STAGEDIR}${PREFIX}/bin/${f}
.endfor

do-test:
	cd ${WRKSRC}/src && GOROOT=${WRKSRC} PATH=${WRKSRC}/bin:${PATH} ${SH} run.bash -no-rebuild -banner ""

pkg-plist: stage
	${RM} ${WRKDIR}/pkg-plist
.for command in go gofmt
	${ECHO_CMD} bin/${command} >> ${WRKDIR}/pkg-plist
.endfor
	cd ${WRKDIR} && ${FIND} go -type f | \
		${SED} -e "s/\/${OPSYS:tl}_${GOARCH}\//\/%%opsys_ARCH%%\//g" | \
		${SORT} >> ${WRKDIR}/pkg-plist
	${CP} ${WRKDIR}/pkg-plist ${.CURDIR}/pkg-plist

.include <bsd.port.post.mk>
