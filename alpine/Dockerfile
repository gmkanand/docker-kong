FROM alpine:3.11

LABEL maintainer="Kong <support@konghq.com>"

ARG ASSET=ce
ENV ASSET $ASSET

ARG EE_PORTS

COPY kong.tar.gz /tmp/kong.tar.gz

ARG KONG_VERSION=2.2.0rc.1
ENV KONG_VERSION $KONG_VERSION

RUN set -eux; \
	arch="$(apk --print-arch)"; \
	case "${arch}" in \
		x86_64) arch='amd64'; KONG_SHA256='e04d8dbd01017f679fb8554142e43cf45ee605b6d75d39fc0d705dda382149e8' ;; \
		aarch64) arch='arm64'; KONG_SHA256='4464513fb1f1c783a444e6027e09007fcb1f04f8c18c17216ce7b17f10d4e2f8' ;; \
	esac; \
    if [ "$ASSET" = "ce" ] ; then \
        apk add --no-cache --virtual .build-deps curl wget tar ca-certificates && \
        curl -fL "https://bintray.com/kong/kong-prerelease/download_file?file_path=kong-$KONG_VERSION.$arch.apk.tar.gz" -o /tmp/kong.tar.gz && \
        echo "$KONG_SHA256  /tmp/kong.tar.gz" | sha256sum -c -; \
        apk del .build-deps; \
    fi; \
    mkdir /kong; \
    tar -C /kong -xzf /tmp/kong.tar.gz && \
    mv /kong/usr/local/* /usr/local && \
    mv /kong/etc/* /etc && \
    rm -rf /kong && \
    apk add --no-cache libstdc++ libgcc openssl pcre perl tzdata libcap zip bash   zlib zlib-dev git ca-certificates && \
    adduser -S kong && \
    mkdir -p "/usr/local/kong" && \
    chown -R kong:0 /usr/local/kong && \
    chown kong:0 /usr/local/bin/kong && \
    chmod -R g=u /usr/local/kong && \
    rm -rf /tmp/kong.tar.gz && \
    if [ "$ASSET" = "ce" ] ; then \
      kong version ; \
    fi;

COPY docker-entrypoint.sh /docker-entrypoint.sh

USER kong

ENTRYPOINT ["/docker-entrypoint.sh"]

EXPOSE 8000 8443 8001 8444 $EE_PORTS

STOPSIGNAL SIGQUIT

CMD ["kong", "docker-start"]
