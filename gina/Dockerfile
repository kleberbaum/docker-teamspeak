FROM alpine:latest

LABEL description "TeamSpeak is a full-featured proprietary VoIP software"

# this fork is maintained by kleberbaum
MAINTAINER Florian Kleber <kleberbaum@erebos.xyz>

# change here to desired version
ARG TEAMSPEAK_CHECKSUM=cb612c26ee18fa0027119056f656ce449caf799f02c0f1864a14b68ea25ed239
ARG TEAMSPEAK_URL=https://files.teamspeak-services.com/releases/server/3.9.1/teamspeak3-server_linux_alpine-3.9.1.tar.bz2

WORKDIR /opt/ts3server

# update, install and cleaning
RUN echo "## Installing base ##" && \
    echo "@main http://dl-cdn.alpinelinux.org/alpine/edge/main/" >> /etc/apk/main && \
    echo "@testing http://dl-cdn.alpinelinux.org/alpine/edge/testing/" >> /etc/apk/repositories && \
    echo "@community http://dl-cdn.alpinelinux.org/alpine/edge/community/" >> /etc/apk/repositories && \
    apk upgrade --update-cache --available && \
    \
    apk add --force \
        bash@main \
        git@main \
        libstdc++@main \
        ca-certificates@main \
        tini@community \
    \
    && echo "## Setup permissions ##" \
    && addgroup -g 9987 ts3server \
    && adduser -u 9987 -Hh /var/ts3server -G ts3server -s /sbin/nologin -D ts3server \
    && mkdir -p /var/ts3server /var/run/ts3server \
    \
    && echo "## Download and install TeamSpeak3 ##" \
    && echo "## Downloading ${TS_VERSION} ##" \
    && wget "${TEAMSPEAK_URL}" -O server.tar.bz2 \
    && echo "Verifying integrity of TeamSpeak..." \
    && echo "${TEAMSPEAK_CHECKSUM} *server.tar.bz2" | sha256sum -c - \
    && echo "All seems good, hash is valid." \
    && tar -xf server.tar.bz2 --strip-components=1 -C /opt/ts3server \
    && rm -rf server.tar.bz2 \
    && mv /opt/ts3server/*.so /opt/ts3server/redist/* /usr/local/lib \
    && rm -rf redist doc serverquerydocs tsdns CHANGELOG \
    && ldconfig /usr/local/lib \
    && chown -R ts3server.ts3server /opt/ts3server /var/ts3server /var/run/ts3server\
    \
    && rm -rf /tmp/* /var/cache/apk/* /var/cache/distfiles/* \
    \
    && echo "set a very secure root password and enable su" \
    && echo "root:ciscocisco" | chpasswd \
    && chmod u+s /bin/su

# 9987 default voice, 10011 server query, 30033 file transport,
# 49152-65535 alternative ports
EXPOSE "9987/udp" "10011/tcp" "30033/tcp"

# setup directory where user data is stored
# /opt/ts3server/ files
# /var/ts3server/ logs
# /var/run/ts3server/ config
VOLUME /opt/ts3server/ /var/ts3server/ /var/run/ts3server/

# deploy init script
COPY docker-entrypoint.sh /opt/ts3server

# starting via tini as init
ENTRYPOINT ["/sbin/tini", "--", "./docker-entrypoint.sh"]

# change to none root user
USER ts3server

# additional post-installation configurations
CMD ["./ts3server", "inifile=/var/run/ts3server/ts3server.ini"]
