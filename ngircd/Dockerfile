#
# builder image
#
FROM alpine:latest as builder

ENV DEV="git gcc g++ make autoconf automake pkgconf patch wget"
#COPY patch.diff /tmp/ngircd.patch

RUN apk update && \
    apk upgrade --update && \
    apk add --no-cache && \
    apk add --no-cache --virtual build_deps $DEV && \
\
    git clone https://github.com/ngircd/ngircd /tmp/ngircd && \
    cd /tmp/ngircd && \
    git checkout rel-26 && \
    wget -O /tmp/ngircd.patch https://raw.githubusercontent.com/himalock/Docker-ngircd/master/ngircd/patch.diff && \
    patch -p1 < /tmp/ngircd.patch && \
    ./autogen.sh && \
    ./configure --enable-ipv6 && \
    make && \
    make install

#
# merge image
#
FROM alpine:latest

COPY --from=builder /usr/local/sbin/ngircd /usr/local/sbin/ngircd
COPY --from=builder /usr/local/etc/ngircd.conf /usr/local/etc/ngircd.conf

#ユーザーで実行しろと怒られるので
RUN addgroup ngircd && \
    adduser -s /bin/sh -S -G ngircd ngircd && \
    touch /usr/local/etc/ngircd.motd && \
    chmod 644 /usr/local/etc/ngircd.*

ENTRYPOINT []

CMD su ngircd -c '/usr/local/sbin/ngircd -n -f /usr/local/etc/ngircd.conf'

EXPOSE 6667
