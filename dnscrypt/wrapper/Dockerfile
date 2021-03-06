FROM alpine:edge
MAINTAINER UmVnaXN0ZXIgcmVnaXN0ZXJkZWRpY2F0ZWQrZG9ja2VyKGF0KWdtYWlsLmNvbQo=

ARG DNSCRYPT_VERSION=0.2.2
ARG DNSCRYPT_URL=https://github.com/Cofyc/dnscrypt-wrapper/archive/v$DNSCRYPT_VERSION.tar.gz
ARG DNSCRYPT_BUILDDIR=dnscrypt-wrapper-$DNSCRYPT_VERSION
ARG DNSCRYPT_PATH=https://github.com/cofyc/dnscrypt-wrapper/commit/fa4fb293589c94d6b59903c8cddb170a0439c52a.diff

ENV RESOLVER_IP     8.8.8.8
ENV RESOLVER_PORT   53
ENV LISTEN_IP       0.0.0.0
ENV LISTEN_PORT     443
ENV PROVIDER_NAME   2.dnscrypt-cert.easypi.info

RUN set -xe && \
    apk add --no-cache --virtual .run-deps \
                                libevent \
                                libsodium && \
    apk add --no-cache --virtual .build-deps \
                                autoconf \
                                bsd-compat-headers \
                                build-base \
                                curl \
                                libevent-dev \
                                libexecinfo-dev \
                                libsodium-dev && \

    curl -sSL $DNSCRYPT_URL | tar xz && \
    cd $DNSCRYPT_BUILDDIR && \
    curl -sSL $DNSCRYPT_PATH | patch -p1 && \
    make configure && \
    ./configure --prefix=/usr && \
    make install && \
    cd .. && \
    rm -rf $$DNSCRYPT_BUILDDIR && \
    apk del .build-deps && \
    mkdir -p /var/lib/dnscrypt-wrapper

COPY entrypoint.sh /usr/bin/entrypoint.sh

WORKDIR /var/lib/dnscrypt-wrapper
VOLUME ["/var/lib/dnscrypt-wrapper"]

EXPOSE $LISTEN_PORT/tcp $LISTEN_PORT/udp

ENTRYPOINT ["/usr/bin/entrypoint.sh"]

CMD dnscrypt-wrapper --resolver-address=${RESOLVER_IP}:${RESOLVER_PORT} \
                     --listen-address=${LISTEN_IP}:${LISTEN_PORT} \
                     --provider-name=${PROVIDER_NAME} \
                     --crypt-secretkey-file=1.key \
                     --provider-cert-file=1.cert \
                     --verbose
