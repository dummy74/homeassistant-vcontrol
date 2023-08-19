# https://developers.home-assistant.io/docs/add-ons/configuration#add-on-dockerfile
ARG BUILD_FROM
FROM $BUILD_FROM

ENV VCONTROLD_VERSION "v0.98.12"

# Execute during the build of the image
ARG TEMPIO_VERSION BUILD_ARCH
RUN \
    curl -sSLf -o /usr/bin/tempio \
    "https://github.com/home-assistant/tempio/releases/download/${TEMPIO_VERSION}/tempio_${BUILD_ARCH}"

RUN \
    apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y build-essential git cmake telnet libxml2-dev mosquitto-clients && \
    git clone --depth 1 --branch $VCONTROLD_VERSION https://github.com/openv/vcontrold.git && \
    cd vcontrold && \
    mkdir build && \
    cd build && \
    cmake -DMANPAGES=OFF .. && \
    make install && \
    make clean && \
    cd .. && \
    cd .. && \
    apt-get purge -y --auto-remove build-essential git cmake && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* vcontrold

# Copy root filesystem
#COPY rootfs /

# Copy data for add-on
ADD rootfs/etc/vcontrold/vcontrold.xml /etc/vcontrold/
ADD rootfs/etc/vcontrold/vito.xml /etc/vcontrold/
ADD rootfs/etc/services.d/vcontrold/run /etc/services.d/vcontrol/run

# Create a user and group for vcontrold
RUN adduser -S -D -h /home/vcontrol vcontrol vcontrol && \
    echo '%wheel ALL=(ALL) ALL' > /etc/sudoers.d/wheel && \
    addgroup vcontrol audio && \
    addgroup vcontrol wheel && \
    addgroup vcontrol dialout
