ARG BUILD_FROM
FROM $BUILD_FROM

MAINTAINER C2BB <advacomp@gmail.com>

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

ENV LANG C.UTF-8

RUN apk update && apk add --no-cache    \
    avahi                               \
    bash jq iw net-tools                \
    sudo busybox-extras                 \
    hostapd networkmanager iptables     \
    networkmanager-cli dnsmasq          \
    linux-firmware-other                \
    linux-firmware-ath6k                \
    linux-firmware-ath10k               \
    linux-firmware-ath9k_htc &&         \
    rm -rf /var/cache/apk/*

COPY hostapd.conf /
COPY udhcpd.conf /etc/udhcpd.conf

COPY run.sh /
RUN chmod a+x /run.sh

CMD [ "/run.sh" ]
