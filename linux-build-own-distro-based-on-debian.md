# Linux: Build own distro based on Debian

Tags: debian, linux

Cài đặt những packages cần thiết để build

```
apt-get install debootstrap dosfstools
```

1. tạo ổ đĩa mới và attach vào debian đang chạy

```
New-VHD –Path \path\to\file.vhdx –SizeBytes 32GB –Dynamic –BlockSizeBytes 1MB
```

2. Phân vùng ổ đĩa với fdisk

```
fdisk /dev/sdX
```

phan vung 1: /dev/sdX1 with type is EFI System

phan vung 2: /dev/sdX2 with type is Linux Filesystem

phan vung 3:/dev/sdX3 with type is Swap

3. Định dạng filesystem cho phân vùng

```
mkfs.vfat /dev/sdX1
mkfs.ext4 /dev/sdX2
mkswap /dev/sdX3
```

```
mkdir /minian
mount /dev/sdb2 /minian
mkdir /minian/boot/efi
mount /dev/sdb1 /minian/boot/efi
```

```
debootstrap --variant=minbase --arch=amd64 stretch /minian http://deb.debian.org/debian/

sed -i -e s/main/"main contrib non-free"/g  /minian/etc/apt/sources.list
```

```
mount --bind /dev /minian/dev
mount --bind /proc /minian/proc
mount --bind /sys /minian/sys
mount -t devpts devpts /minian/dev/pts

cp /etc/hosts /minian/etc/hosts
mkdir -p /minian/etc/network/interfaces.d
cp /etc/network/interfaces /minian/etc/network/

chroot /minian /bin/bash

# do not install documentation to keep image small

cat >
path-exclude=/usr/share/man/*
path-exclude=/usr/share/doc/*
path-exclude=/usr/share/groff/*
path-exclude=/usr/share/info/*
path-exclude=/usr/share/lintian/*
path-exclude=/usr/share/linda/*
path-exclude=/usr/share/locale/*
EOF

apt-get update
apt-get install --no-install-recommends linux-image-amd64 grub-efi-amd64 firmware-linux systemd-sysv wget nano dialog apt-utils iputils-ping netbase iproute2 ifupdown isc-dhcp-client sudo openssh-server

grub-install /dev/sdb
update-grub

adduser long
usermod -aG sudo long

cat  > /etc/fstab <<EOF
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
UUID=$( blkid -s UUID -o value /dev/sdb2 )    /            ext4    errors=remount-ro    0    1
UUID=$( blkid -s UUID -o value /dev/sdb1 )    /boot/efi    vfat    umask=0077    0    1
UUID=$( blkid -s UUID -o value /dev/sdb3 )    none         swap    sw    0    0
EOF
```
