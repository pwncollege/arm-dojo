from ubuntu:24.04

run dpkg --add-architecture arm64
copy ./docker-files/ubuntu.sources /etc/apt/sources.list.d/ubuntu.sources
#copy ./docker-files/sources.list /etc/apt/sources.list
run apt-get update && apt-get upgrade -y

# install qemu
run apt-get install -y qemu-user-static libc6-dev-amd64-cross binutils-aarch64-linux-gnu
run apt-get install -y libc6:arm64 libcapstone-dev:arm64

# install tools
run apt-get install -y gdb-multiarch sudo


