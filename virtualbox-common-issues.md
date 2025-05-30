# Virtualbox: Common issues

Tags: virtualbox

**1. intel\_rapl: no valid rapl domains found in package 0**

```
echo 'blacklist intel_rapl' >> /etc/modprobe.d/blacklist.conf
update-initramfs -u -k all
```

**2. piix4\_smbus 0000.00.07.0: SMBus base address uninitialized - upgrade bios or use force\_addr=0xaddr**

```
lsmod | grep i2c_piix4
echo 'blacklist i2c_piix4' >> /etc/modprobe.d/blacklist.conf
update-initramfs -u -k all
```
