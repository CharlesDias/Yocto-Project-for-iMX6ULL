# Docker name 
# charlesdias/yocto_imx6ull_bsp6
#
# docker build --no-cache --build-arg "host_uid=$(id -u)" --build-arg "host_gid=$(id -g)" -t charlesdias/yocto_imx6ull_bsp6:<version> -t charlesdias/yocto_imx6ull_bsp6:latest .
#
# docker push charlesdias/yocto_imx6ull_bsp6 --all-tags

FROM ubuntu:18.04

LABEL maintainer="Charles Dias <charlesdias.cd@outlook.com>"

# Set the timezone.
ENV TZ=America/Fortaleza
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Yocto build dependencies
RUN apt-get update && apt-get -y upgrade && \
    apt-get -y install gawk wget git diffstat unzip texinfo gcc \
    build-essential chrpath socat cpio python3 python3-pip python3-pexpect \
    xz-utils debianutils iputils-ping python3-git python3-jinja2 \
    libegl1-mesa libsdl1.2-dev pylint3 xterm python3-subunit mesa-common-dev \
    zstd liblz4-tool \
    curl qemu bison ca-certificates \
    ccache check flex git-core gperf lcov libncurses-dev libusb-1.0-0-dev \
    make ninja-build zip python3-distutils gcc-multilib vim \
    rsync bc tmux git-lfs parted libgmp3-dev libmpc-dev

# Set up locales
RUN apt-get -y install locales locales-all apt-utils sudo && dpkg-reconfigure locales && \
    locale-gen en_US.UTF-8 && update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8 
ENV LANG en_US.UTF-8
ENV LC_ALL en_US.UTF-8
ENV LANGUAGE en_US.UTF-8

# Clean up APT when done.
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# In Ubuntu, /bin/sh is a link to /bin/dash. The dash shell does not support 
# the source command. Replace dash with bash
RUN rm /bin/sh && ln -s bash /bin/sh

# Set environment variables, which are used later in the Dockerfile.
ENV USER_NAME imx6ull
ENV PROJECT imx6ull

ARG host_uid=1000
ARG host_gid=1000
RUN groupadd -g $host_gid $USER_NAME && \
    useradd -g $host_gid -m -s /bin/bash -u $host_uid $USER_NAME

USER $USER_NAME

# Install repo
RUN mkdir ~/bin && curl https://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo && chmod a+x ~/bin/repo

ENV PATH="~/bin:${PATH}"

# Install toolchain GCC Linaro
RUN cd /home/$USER_NAME/ && wget -O gcc-arm-9.2-2019.12-x86_64-arm-none-linux-gnueabihf.tar.xz "https://developer.arm.com/-/media/Files/downloads/gnu-a/9.2-2019.12/binrel/gcc-arm-9.2-2019.12-x86_64-arm-none-linux-gnueabihf.tar.xz?revision=fed31ee5-2ed7-40c8-9e0e-474299a3c4ac&la=en&hash=76DAF56606E7CB66CC5B5B33D8FB90D9F24C9D20" && \
    tar xvf gcc-arm-9.2-2019.12-x86_64-arm-none-linux-gnueabihf.tar.xz && \
    ln -s gcc-arm-9.2-2019.12-x86_64-arm-none-linux-gnueabihf gcc-linaro && \
    rm gcc-arm-9.2-2019.12-x86_64-arm-none-linux-gnueabihf.tar.xz

ENV ARCH=arm \
    DTC_FLAGS="-@" \
    PATH=~/gcc-linaro/bin/:$PATH \
    CROSS_COMPILE="arm-linux-gnueabihf-"

RUN mkdir ${HOME}/oe-core && cd ${HOME}/oe-core

WORKDIR /home/$USER_NAME/oe-core