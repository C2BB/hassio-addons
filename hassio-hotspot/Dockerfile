ARG BUILD_FROM
FROM $BUILD_FROM

LABEL João Loureiro <joaofl@gmail.com>

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

ENV LANG C.UTF-8







# install required packages to build "alpine linux" packages
RUN apk add --update --no-cache --no-progress alpine-sdk coreutils bash
RUN apk add --update --no-cache --no-progress sudo

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
VOLUME /home/user1/aports
WORKDIR /home/user1/aports
RUN chown user1:user1 /home/user1/aports

# make user1 the current user in the image
USER user1

# create keys for signing packages after the build has been finished
RUN abuild-keygen -a -i -n

# setup git for the build user
RUN git config --global user.name "Build User"
RUN git config --global user.email "build-user@example.com"

RUN git clone git://git.alpinelinux.org/aports

WORKDIR /home/user1/aports/aports/non-free/b43-firmware

RUN abuild -r
RUN apk add --allow-untrusted ~/packages/...pkg
RUN apk add b43-fwcutter b43-firmware
RUN modprobe b43
RUN echo b43 >> /etc/modules





# Install requirements for add-on
RUN apk update && apk add --no-cache    \
    avahi                               \
    bash jq iw net-tools                \
    sudo busybox-extras                 \
    hostapd networkmanager iptables     \
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
