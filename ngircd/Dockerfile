FROM alpine:latest

ENV DEV="git gcc g++ make autoconf automake pkgconf patch"

COPY patch.diff /tmp/ngircd.patch

RUN apk update && \
    apk upgrade --update && \
    apk add --no-cache && \
    apk add --no-cache --virtual build_deps $DEV && \
\
    git clone https://github.com/ngircd/ngircd /tmp/ngircd && \
    cd /tmp/ngircd && \
    patch -p1 < /tmp/ngircd.patch && \
    ./autogen.sh && \
    ./configure --enable-ipv6 && \
    make && \
    make install && \
    #ユーザーで実行しろと怒られた
    addgroup ngircd && \
    adduser -s /bin/sh -S -G ngircd ngircd && \
    touch /usr/local/etc/ngircd.motd && \
    chmod 644 /usr/local/etc/ngircd.* && \
# 不要なパッケージを削除
    apk del --purge build_deps && \
\
# 掃除
    rm -rf /var/cache/apk/* && \
    rm -rf /var/lib/apt/lists/* && \
    rm -rf /tmp/*

ENTRYPOINT []

CMD su ngircd -c '/usr/local/sbin/ngircd -n -f /usr/local/etc/ngircd.conf'

EXPOSE 6667
