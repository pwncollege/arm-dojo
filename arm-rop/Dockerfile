from ubuntu:24.04 as build

run apt-get update && apt-get upgrade -y
run DEBIAN_FRONTEND=noninteractive apt install -y flex bison build-essential wget python3 python3-pip ninja-build pkg-config libglib2.0-dev debhelper-compat meson texinfo python3-sphinx  python3-sphinx-rtd-theme zlib1g-dev libaio-dev libjack-dev libpulse-dev libasound2-dev libpipewire-0.3-dev libbpf-dev libbrlapi-dev libcap-ng-dev libcurl4-gnutls-dev libfdt-dev libfuse3-dev gnutls-dev libdrm-dev libnfs-dev
workdir /root
run wget https://download.qemu.org/qemu-9.1.0.tar.xz && tar xvf ./qemu-9.1.0.tar.xz && rm qemu-9.1.0.tar.xz
workdir /root/qemu-9.1.0
run mkdir build && cd build && ../configure --static --disable-system --enable-linux-user && make -j$(nproc)

from ubuntu:24.04

run dpkg --add-architecture arm64
copy ./docker-files/ubuntu.sources /etc/apt/sources.list.d/ubuntu.sources
run apt-get update && apt-get upgrade -y

# install qemu
copy --from=build /root/qemu-9.1.0/build/qemu-aarch64 /usr/bin/qemu-aarch64-static
run apt-get install -y libc6-dev-amd64-cross binutils-aarch64-linux-gnu
run apt-get install -y libc6:arm64 libcapstone-dev:arm64

# install tools
run apt-get install -y gdb-multiarch sudo file gcc-aarch64-linux-gnu tmux screen byobu

# Install rop stuff
# run apt-get install -y python3-pip python3-capstone python3-filebytes python3-keystone-engine python3-ropper python3-pyvex



