FROM alpine:latest

LABEL description "TeamSpeak is a full-featured proprietary VoIP software"

# this fork is maintained by kleberbaum
MAINTAINER Florian Kleber <kleberbaum@erebos.xyz>

# change here to desired version
ARG TEAMSPEAK_URL "http://dl.4players.de/ts/releases/pre_releases/server/3.3.0-Beta-1/teamspeak3-server_linux_alpine-3.3.0.tar.bz2"
ARG TEAMSPEAK_CHECKSUM "25af7f8ba48199fa075102727c763a510b69b4d098afb77b0d6da1c1c6103fd1"

WORKDIR /opt/ts3server

# update, install and cleaning
RUN echo "## Installing base ##" && \
    echo "@testing http://dl-cdn.alpinelinux.org/alpine/edge/testing/" >> /etc/apk/repositories && \
    echo "@community http://dl-cdn.alpinelinux.org/alpine/edge/community/" >> /etc/apk/repositories && \
    apk upgrade --update-cache --available && \
    \
    apk add --force \
        su-exec \
        libstdc++ \
        ca-certificates \
        tini@community \
    \
    && addgroup -g 9987 ts3server \
    && adduser -u 9987 -Hh /var/ts3server -G ts3server -s /sbin/nologin -D ts3server \
    && mkdir -p /var/ts3server /var/run/ts3server \
    && chown ts3server.ts3server /var/ts3server /var/run/ts3server \
    && echo "## Downloading ${TS_VERSION} ##" \
    && apk add --no-cache --virtual=build-dependencies tar \
    && curl -o server.tar.bz2 -SL "${TEAMSPEAK_URL}" \
    && echo "${TEAMSPEAK_CHECKSUM} *server.tar.bz2" | sha256sum -c - \
    && tar -xf server.tar.bz2 --strip-components=1 -C /opt/ts3server \
    && rm -rf server.tar.bz2 \
    && mv /opt/ts3server/*.so /opt/ts3server/redist/* /usr/local/lib \
    && rm -rf redist doc serverquerydocs tsdns CHANGELOG \
    && ldconfig /usr/local/lib \
    && chown -R ts3server.ts3server /opt/ts3server \
    \
    && apk del --purge --no-cache build-dependencies \
    && rm -rf /tmp/* /var/cache/apk/* /var/cache/distfiles/*

# 9987 default voice, 10011 server query, 30033 file transport
EXPOSE "9987/udp" "10011/tcp" "30033/tcp"

# place init
ADD run.sh /
RUN chmod +x /run.sh

# I personally like to start my containers with tini ^^
ENTRYPOINT ["/sbin/tini", "--", "/run.sh"]

# additional post-installation configurations
CMD ["ts3server"]
