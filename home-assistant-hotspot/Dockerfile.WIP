ARG BUILD_FROM
FROM $BUILD_FROM
#FROM alpine:latest

# install required packages to build "alpine linux" packages
RUN apk add --update --no-cache --no-progress alpine-sdk coreutils bash
RUN apk add --update --no-cache --no-progress sudo
RUN apk add --update --no-cache --no-progress b43-fwcutter

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]
ENV LANG C.UTF-8


# setup directory for built packages
RUN mkdir -p /var/cache/distfiles
RUN chmod a+w /var/cache/distfiles
RUN chgrp abuild /var/cache/distfiles
RUN chmod g+w /var/cache/distfiles

# setup the abuild configuration
RUN echo 'PACKAGER="Your Name <your@email.address>"' >> /etc/abuild.conf
RUN echo 'MAINTAINER="$PACKAGER"' >> /etc/abuild.conf
RUN echo "%abuild ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers.d/abuild

# setup the build user in container
RUN adduser -D user1
RUN addgroup user1 abuild

# setup directory to pass in build instructions files for abuild
WORKDIR /home/user1

# make user1 the current user in the image
USER user1

# create keys for signing packages after the build has been finished
RUN abuild-keygen -a -i -n

# setup git for the build user
RUN git config --global user.name "Build User"
RUN git config --global user.email "build-user@example.com"

RUN git clone --depth 1 -b 3.15-stable git://git.alpinelinux.org/aports /home/user1/aports/


WORKDIR /home/user1/aports/non-free/b43-firmware

RUN abuild -r

USER root

RUN apk add --allow-untrusted /home/user1/packages/non-free/x86_64/b43-firmware-4.150.10.5-r1.apk
RUN apk add b43-firmware



# Install requirements for add-on
RUN apk update && apk add --no-cache    \
    avahi                               \
    bash jq iw net-tools                \
    sudo busybox-extras                 \
    hostapd networkmanager iptables     \
    linux-firmware-other                \
    linux-firmware-ath6k                \
    linux-firmware-ath10k               \
    linux-firmware-brcm               \
    linux-firmware-ath9k_htc &&         \
    rm -rf /var/cache/apk/*

COPY hostapd.conf /
COPY udhcpd.conf /etc/udhcpd.conf
COPY run.sh /
RUN echo b43 >> /etc/modules 
RUN chmod a+x /run.sh

CMD [ "/run.sh" ]
