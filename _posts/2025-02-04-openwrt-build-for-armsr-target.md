---
title: OpenWRT Build - armsr
description: >-
  This blog will help you to trigger OpenWRT build for ARM System Ready target(armsr)
author: Dinesh_Loganthan
date: 2025-02-04 10:00:00 +0500
categories: [Blogging, Tutorial]
tags: [openwrt]
pin: true
---

## ARM System Ready Target

* OpenWRT replaced armvirt target by armsr

* armsr system adds support for Arm systems that support booting EFI images

* Reference - [openwrt target - armsr](https://github.com/openwrt/openwrt/tree/main/target/linux/armsr)

## Build Instructions - armsr - armv8

### 1. OpenWRT : Repo Init

* Clone OpenWRT repo for armsr target build using the below command

```bash
git clone https://github.com/openwrt/openwrt.git

cd openwrt/
```

### 2. Updating feeds

* openwrt feeds can be cloned and updated in the openwrt/feeds directory using the below command

```bash
./scripts/feeds update -a
```

* Updated feeds and packages can be installed in the package directory for openwrt to identify and compile the package using the below command

```bash
./scripts/feeds install -a
```

### 3. Selecting the Target

* Select and save armsr target profile using the **make menuconfig** command in the below sequence

```
1. Target System -> Arm SystemReady (EFI) compliant
2. Subtarget -> 64-bit (armv8) machines
3. Target Profile -> Generic EFI Boot
4. Save -> .config
5. Exit -> Yes
```

### 4. Trigger build

* Trigger openwrtr build using the below command

```bash
make V=e -j8
```

## armsr - armv8 : snapshot Images

* Latest Image for Arm System Ready armv8 Target are available in snapshot downloads - [armsr - armv8](https://downloads.openwrt.org/snapshots/targets/armsr/armv8/)

## QEMU Instructions for Running Images

* Qemu can be used to emulate the armsr - armv8 target using the below command

```
cd bin/targets/armsr/armv8/

gunzip -c openwrt-armsr-armv8-generic-squashfs-combined-efi.img.gz > openwrt-arm-64.img

qemu-system-aarch64 -nographic \
    -cpu cortex-a53 -machine virt \
    -bios u-boot-qemu_armv8/u-boot.bin \
    -smp 1 -m 1024 \
    -device virtio-rng-pci \
    -drive file=openwrt-arm-64.img,format=raw,index=0,media=disk \
    -netdev user,id=testlan -net nic,netdev=testlan \
    -netdev user,id=testwan -net nic,netdev=testwan 

```

### OpenWRT bootlog : QEMU ARM virtual machines

* Running QEMU ARM virtual machines for 64-bit target using qemu-system-aarch64

* **qemu-system-aarch64 -bios** argument can be used to boot the target from u-boot

```bash
dinesh@linux:[armsr-armv8]$ qemu-system-aarch64 -nographic \
    -cpu cortex-a53 -machine virt \
    -bios u-boot-qemu_armv8/u-boot.bin \
    -smp 1 -m 1024 \
    -device virtio-rng-pci \
    -drive file=openwrt-arm-64.img,format=raw,index=0,media=disk \
    -netdev user,id=testlan -net nic,netdev=testlan \
    -netdev user,id=testwan -net nic,netdev=testwan 


U-Boot 2023.04-OpenWrt-r28739-69890e16b3 (Feb 01 2025 - 05:06:59 +0000)

DRAM:  1 GiB
Core:  51 devices, 14 uclasses, devicetree: board
Flash: 64 MiB
Loading Environment from Flash... *** Warning - bad CRC, using default environment

In:    pl011@9000000
Out:   pl011@9000000
Err:   pl011@9000000
Net:   eth0: virtio-net#32, eth1: virtio-net#33
Hit any key to stop autoboot:  0 
Scanning for bootflows in all bootdevs
Seq  Method       State   Uclass    Part  Name                      Filename
---  -----------  ------  --------  ----  ------------------------  ----------------
Scanning global bootmeth 'efi_mgr':
Scanning bootdev 'fw-cfg@9020000.bootdev':
fatal: no kernel available
No working controllers found
scanning bus for devices...
Unknown seq -1 for label 'scsi'
Scanning bootdev 'virtio-blk#35.bootdev':
  0  efi          ready   virtio       1  virtio-blk#35.bootdev.par efi/boot/bootaa64.efi
** Booting bootflow 'virtio-blk#35.bootdev.part_1' with efi
Failed to load EFI variables
Missing TPMv2 device for EFI_TCG_PROTOCOL
Booting /efi\boot\bootaa64.efi
error: serial port `com0' isn't found.
error: terminal `serial' isn't found.
error: terminal `serial' isn't found.


                                                                        GNU GRUB  version 2.12

 ┌──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
 │*OpenWrt                                                                                                                                                          │ 
 │ OpenWrt (failsafe)                                                                                                                                               │
 │                                                                                                                                                                  │
 │                                                                                                                                                                  │
 │                                                                                                                                                                  │
 │                                                                                                                                                                  │ 
 └──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘

      Use the ▲ and ▼ keys to select which entry is highlighted.
      Press enter to boot the selected OS, `e' to edit the commands before booting or `c' for a command-line. ESC to return previous menu.


EFI stub: Booting Linux Kernel...
EFI stub: Using DTB from configuration table
EFI stub: Exiting boot services...
[    0.000000] Booting Linux on physical CPU 0x0000000000 [0x410fd034]
[    0.000000] Linux version 6.6.74 (builder@buildhost) (aarch64-openwrt-linux-musl-gcc (OpenWrt GCC 13.3.0 r28739-69890e16b3) 13.3.0, GNU ld (GNU Binutils) 2.42) #0 SMP Sat Feb  1 05:06:59 2025
[    0.000000] KASLR enabled
[    0.000000] Machine model: linux,dummy-virt
[    0.000000] efi: EFI v2.10 by Das U-Boot
[    0.000000] efi: RTPROP=0x7dd99040 SMBIOS=0x7dd95000 RNG=0x7dcd7040 MEMRESERVE=0x7a7f0040 
[    0.000000] random: crng init done
[    0.000000] earlycon: pl11 at MMIO 0x0000000009000000 (options '')
[    0.000000] printk: bootconsole [pl11] enabled
[    0.000000] NUMA: No NUMA configuration found
[    0.000000] NUMA: Faking a node at [mem 0x0000000040000000-0x000000007fffffff]
[    0.000000] NUMA: NODE_DATA [mem 0x7fddd380-0x7fddffff]
[    0.000000] Zone ranges:
[    0.000000]   DMA      [mem 0x0000000040000000-0x000000007fffffff]
[    0.000000]   DMA32    empty
[    0.000000]   Normal   empty
[    0.000000] Movable zone start for each node
[    0.000000] Early memory node ranges
[    0.000000]   node   0: [mem 0x0000000040000000-0x000000007dd94fff]
[    0.000000]   node   0: [mem 0x000000007dd95000-0x000000007dd95fff]
[    0.000000]   node   0: [mem 0x000000007dd96000-0x000000007dd98fff]
[    0.000000]   node   0: [mem 0x000000007dd99000-0x000000007dd99fff]
[    0.000000]   node   0: [mem 0x000000007dd9a000-0x000000007dd9afff]
[    0.000000]   node   0: [mem 0x000000007dd9b000-0x000000007dda0fff]
[    0.000000]   node   0: [mem 0x000000007dda1000-0x000000007feeffff]
[    0.000000]   node   0: [mem 0x000000007fef0000-0x000000007fefffff]
[    0.000000]   node   0: [mem 0x000000007ff00000-0x000000007fffffff]
[    0.000000] Initmem setup node 0 [mem 0x0000000040000000-0x000000007fffffff]
[    0.000000] cma: Reserved 32 MiB at 0x000000007bc00000 on node -1
[    0.000000] psci: probing for conduit method from DT.
[    0.000000] psci: PSCIv0.2 detected in firmware.
[    0.000000] psci: Using standard PSCI v0.2 function IDs
[    0.000000] psci: Trusted OS migration not required
[    0.000000] percpu: Embedded 20 pages/cpu s42664 r8192 d31064 u81920
[    0.000000] Detected VIPT I-cache on CPU0
[    0.000000] CPU features: kernel page table isolation forced ON by KASLR
[    0.000000] CPU features: detected: Kernel page table isolation (KPTI)
[    0.000000] CPU features: detected: ARM erratum 843419
[    0.000000] alternatives: applying boot alternatives
[    0.000000] Kernel command line: BOOT_IMAGE=/efi/openwrt/vmlinux root=PARTUUID=b2b6a5ad-6fce-6bc1-c923-c5c374ad6802 rootwait earlycon console=tty1 noinitrd
[    0.000000] Unknown kernel command line parameters "BOOT_IMAGE=/efi/openwrt/vmlinux", will be passed to user space.
[    0.000000] Dentry cache hash table entries: 131072 (order: 8, 1048576 bytes, linear)
[    0.000000] Inode-cache hash table entries: 65536 (order: 7, 524288 bytes, linear)
[    0.000000] Fallback order for Node 0: 0 
[    0.000000] Built 1 zonelists, mobility grouping on.  Total pages: 258048
[    0.000000] Policy zone: DMA
[    0.000000] mem auto-init: stack:off, heap alloc:off, heap free:off
[    0.000000] software IO TLB: SWIOTLB bounce buffer size adjusted to 1MB
[    0.000000] software IO TLB: area num 1.
[    0.000000] software IO TLB: mapped [mem 0x000000007eb00000-0x000000007ec00000] (1MB)
[    0.000000] Memory: 971548K/1048576K available (11776K kernel code, 1554K rwdata, 4336K rodata, 3584K init, 486K bss, 44260K reserved, 32768K cma-reserved)
[    0.000000] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=1, Nodes=1
[    0.000000] rcu: Hierarchical RCU implementation.
[    0.000000] rcu:     RCU restricting CPUs from NR_CPUS=256 to nr_cpu_ids=1.
[    0.000000]  Tracing variant of Tasks RCU enabled.
[    0.000000] rcu: RCU calculated value of scheduler-enlistment delay is 10 jiffies.
[    0.000000] rcu: Adjusting geometry for rcu_fanout_leaf=16, nr_cpu_ids=1
[    0.000000] NR_IRQS: 64, nr_irqs: 64, preallocated irqs: 0
[    0.000000] Root IRQ handler: gic_handle_irq
[    0.000000] GICv2m: range[mem 0x08020000-0x08020fff], SPI[80:143]
[    0.000000] rcu: srcu_init: Setting srcu_struct sizes based on contention.
[    0.000000] arch_timer: cp15 timer(s) running at 62.50MHz (virt).
[    0.000000] clocksource: arch_sys_counter: mask: 0x1ffffffffffffff max_cycles: 0x1cd42e208c, max_idle_ns: 881590405314 ns
[    0.000053] sched_clock: 57 bits at 63MHz, resolution 16ns, wraps every 4398046511096ns
[    0.002001] Console: colour dummy device 80x25
[    0.002252] printk: console [tty1] enabled
[    0.002530] printk: bootconsole [pl11] disabled
Please press Enter to activate this console.


BusyBox v1.37.0 (2025-02-01 05:06:59 UTC) built-in shell (ash)

  _______                     ________        __
 |       |.-----.-----.-----.|  |  |  |.----.|  |_
 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
 |_______||   __|_____|__|__||________||__|  |____|
          |__| W I R E L E S S   F R E E D O M
 -----------------------------------------------------
 OpenWrt SNAPSHOT, r28739-69890e16b3
 -----------------------------------------------------
=== WARNING! =====================================
There is no root password defined on this device!
Use the "passwd" command to set up a new password
in order to prevent unauthorized SSH logins.
--------------------------------------------------

 OpenWrt recently switched to the "apk" package manager!

 OPKG Command           APK Equivalent      Description
 ------------------------------------------------------------------
 opkg install <pkg>     apk add <pkg>       Install a package
 opkg remove <pkg>      apk del <pkg>       Remove a package
 opkg upgrade           apk upgrade         Upgrade all packages
 opkg files <pkg>       apk info -L <pkg>   List package contents
 opkg list-installed    apk info            List installed packages
 opkg update            apk update          Update package lists
 opkg search <pkg>      apk search <pkg>    Search for packages
 ------------------------------------------------------------------

For more https://openwrt.org/docs/guide-user/additional-software/opkg-to-apk-cheatsheet

root@OpenWrt:~# 
```

### OpenWRT version and board details

```bash
root@OpenWrt:~# 
root@OpenWrt:~# cat /tmp/sysinfo/board_name 
linux,dummy-virt
root@OpenWrt:~# 
root@OpenWrt:~# cat /etc/openwrt_release 
DISTRIB_ID='OpenWrt'
DISTRIB_RELEASE='SNAPSHOT'
DISTRIB_REVISION='r28739-69890e16b3'
DISTRIB_TARGET='armsr/armv8'
DISTRIB_ARCH='aarch64_generic'
DISTRIB_DESCRIPTION='OpenWrt SNAPSHOT r28739-69890e16b3'
DISTRIB_TAINTS=''
root@OpenWrt:~# 
root@OpenWrt:~# cat /etc/openwrt_version 
r28739-69890e16b3
root@OpenWrt:~# 
```

### Network Verification:

* WAN IP address is getting assigned to eth1 interface in the ifconfig command output

* Ping verification to 8.8.8.8 and google.com from the openwrt target is successful

```bash
root@OpenWrt:~# 

root@OpenWrt:~# ifconfig 
br-lan    Link encap:Ethernet  HWaddr 52:54:00:12:34:56  
          inet addr:192.168.1.1  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::5054:ff:fe12:3456/64 Scope:Link
          inet6 addr: fd4a:235:42a0::1/60 Scope:Global
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:2 errors:0 dropped:0 overruns:0 frame:0
          TX packets:18 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:604 (604.0 B)  TX bytes:2708 (2.6 KiB)

eth0      Link encap:Ethernet  HWaddr 52:54:00:12:34:56  
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:4 errors:0 dropped:0 overruns:0 frame:0
          TX packets:30 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:852 (852.0 B)  TX bytes:7552 (7.3 KiB)

eth1      Link encap:Ethernet  HWaddr 52:54:00:12:34:57  
          inet addr:10.0.2.15  Bcast:10.0.2.255  Mask:255.255.255.0
          inet6 addr: fe80::5054:ff:fe12:3457/64 Scope:Link
          inet6 addr: fec0::5054:ff:fe12:3457/64 Scope:Site
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:31 errors:0 dropped:0 overruns:0 frame:0
          TX packets:46 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:3679 (3.5 KiB)  TX bytes:5138 (5.0 KiB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:33 errors:0 dropped:0 overruns:0 frame:0
          TX packets:33 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:3713 (3.6 KiB)  TX bytes:3713 (3.6 KiB)

root@OpenWrt:~# 
root@OpenWrt:~# ping -c 5 8.8.8.8
PING 8.8.8.8 (8.8.8.8): 56 data bytes
64 bytes from 8.8.8.8: seq=0 ttl=255 time=3.896 ms
64 bytes from 8.8.8.8: seq=1 ttl=255 time=8.914 ms
64 bytes from 8.8.8.8: seq=2 ttl=255 time=2.969 ms
64 bytes from 8.8.8.8: seq=3 ttl=255 time=4.422 ms
64 bytes from 8.8.8.8: seq=4 ttl=255 time=3.197 ms

--- 8.8.8.8 ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max = 2.969/4.679/8.914 ms
root@OpenWrt:~# 
root@OpenWrt:~# ping -c 5 google.com
PING google.com (216.58.196.174): 56 data bytes
64 bytes from 216.58.196.174: seq=0 ttl=255 time=2.755 ms
64 bytes from 216.58.196.174: seq=1 ttl=255 time=3.934 ms
64 bytes from 216.58.196.174: seq=2 ttl=255 time=4.300 ms
64 bytes from 216.58.196.174: seq=3 ttl=255 time=3.891 ms
64 bytes from 216.58.196.174: seq=4 ttl=255 time=4.252 ms

--- google.com ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max = 2.755/3.826/4.300 ms
```
