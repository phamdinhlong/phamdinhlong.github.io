# Virtualbox: Common commands

Tags: virtualbox

**Start a VM in Headless mode**

```
# Windows host
C:\Program Files\Oracle\VirtualBox\VBoxManage startvm "VM name" --type headless
```

**Compacting VDI**

```
# 1. In linux guest
sudo dd if=/dev/zero of=zerofillfile bs=1M
sudo rm zerofillfile

Open cmd.exe
C:\Program Files\Oracle\VirtualBox\VBoxManage modifymedium --compact "path\to\filename.vdi"
```

**Converting VDI to VHD**

```
C:\Program Files\Oracle\VirtualBox\VBoxManage clonemedium path\to\filename.vdi path\to\filename.vhd --format VHD
```

**Converting VHDX to VDI**

```
C:\Program Files\Oracle\VirtualBox\VBoxManage clonemedium \path\to\file.vhdx \path\to\file.vdi --format VDI
```
