# syntax=docker/dockerfile:1

FROM ubuntu:24.04 AS build
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        flex bison build-essential wget python3 python3-pip ninja-build pkg-config \
        libglib2.0-dev debhelper-compat meson texinfo \
        python3-sphinx python3-sphinx-rtd-theme zlib1g-dev libaio-dev libjack-dev \
        libpulse-dev libasound2-dev libpipewire-0.3-dev libbpf-dev libbrlapi-dev \
        libcap-ng-dev libcurl4-gnutls-dev libfdt-dev libfuse3-dev gnutls-dev \
        libdrm-dev libnfs-dev && \
    rm -rf /var/lib/apt/lists/*

RUN <<EOF
    cd /root
    wget https://download.qemu.org/qemu-9.1.0.tar.xz
    tar xf qemu-9.1.0.tar.xz
    rm qemu-9.1.0.tar.xz
    cd qemu-9.1.0
    mkdir build
    cd build
    ../configure --static --disable-system --enable-linux-user
    make -j"$(nproc)"
EOF

# Stage 2: Final image
FROM ubuntu:24.04
ENV DEBIAN_FRONTEND=noninteractive

RUN dpkg --add-architecture arm64

COPY <<EOF /etc/apt/sources.list.d/ubuntu.sources
# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.

## Ubuntu distribution repository
Types: deb
URIs: http://archive.ubuntu.com/ubuntu/
Suites: noble noble-updates noble-backports
Components: main universe restricted multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
Architectures: amd64

## Ubuntu security updates.
Types: deb
URIs: http://security.ubuntu.com/ubuntu/
Suites: noble-security
Components: main universe restricted multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
Architectures: amd64

## Ubuntu ports repository for arm64 architecture.
Types: deb
URIs: http://ports.ubuntu.com/ubuntu-ports/
Suites: noble noble-updates noble-backports noble-security
Components: main universe restricted multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
Architectures: arm64
EOF

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        libc6-dev-amd64-cross binutils-aarch64-linux-gnu \
        libc6:arm64 libcapstone-dev:arm64 \
        gdb-multiarch sudo file gcc-aarch64-linux-gnu \
        tmux screen byobu rsync && \
    rm -rf /var/lib/apt/lists/*

COPY --from=build /root/qemu-9.1.0/build/qemu-aarch64 /usr/bin/qemu-aarch64-static

RUN usermod -md /home/hacker -l hacker ubuntu && \
    groupmod -n hacker ubuntu

ADD --chown=0:0 --chmod=6755 https://github.com/pwncollege/exec-suid/releases/latest/download/exec-suid /usr/bin/exec-suid
