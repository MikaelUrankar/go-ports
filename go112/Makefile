# $FreeBSD$

# bootstrap generated on amd64 with:
# env GOOS=freebsd GOARCH=ppc64 GOROOT_BOOTSTRAP=/usr/local/go src/make.bash (elfv1)
# env GOOS=freebsd GOARCH=arm64 GOROOT_BOOTSTRAP=/usr/local/go src/make.bash
# Starting in go1.9, the minimum processor requirement for ppc64 is POWER8:
# https://github.com/golang/go/issues/19074

PORTNAME=	go112
PORTVERSION=	1.12.4
CATEGORIES=	lang
# XXX should be hosted somewhere else
MASTER_SITES=	http://mikael.urankar.free.fr/FreeBSD/go/
DISTNAME=	go-freebsd-${GOARCH}-bootstrap

MAINTAINER=	ports@FreeBSD.org
COMMENT=	Go bootstrap for aarch64

LICENSE=	BSD3CLAUSE

ONLY_FOR_ARCHS=	aarch64 powerpc64

NO_BUILD=	yes

GOARCH=		${ARCH:S/aarch64/arm64/:S/powerpc64/ppc64/}

PLIST_SUB=	GOARCH=${GOARCH}

.include <bsd.port.pre.mk>
.if ${ARCH} == aarch64
PLIST_SUB+=	AARCH64=""
PLIST_SUB+=	PPC64="@comment "
.else
PLIST_SUB+=	AARCH64="@comment "
PLIST_SUB+=	PPC64=""
.endif

INSTALLDIR=	${STAGEDIR}/${PREFIX}/${PORTNAME}

do-install:
	@cd ${WRKSRC} && ${COPYTREE_SHARE} . ${INSTALLDIR}
	@cd ${WRKSRC} && ${COPYTREE_BIN} bin ${INSTALLDIR}
	@cd ${WRKSRC}/pkg/tool && ${COPYTREE_BIN} freebsd_${GOARCH} ${INSTALLDIR}/pkg/tool
	@${RM} -rf ${INSTALLDIR}/src/debug

.include <bsd.port.post.mk>
