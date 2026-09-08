---
layout: post
title: "BenixOS boots on real virtualized hardware, repeatably"
date: 2026-09-07
categories: [benixos]
tags: [benixos, thunderhead, yocto, ci, proxmox, virtio, wic]
excerpt: "The Thunderhead-AMD64 artifact chain — build, publish, deploy, boot — is now proven on a real Proxmox VM with real DHCP networking, three consecutive green builds running. A concrete step past boot-verified in QEMU."
---

The BenixOS Thunderhead-AMD64 canary was previously boot-verified in
QEMU: a real `.wic`, GRUB EFI, a login prompt. That was the right first
milestone. It is no longer where the pipeline stops.

**What's proven now**: three consecutive green builds of
`BenixOS-Thunderhead-AMD64` — #26, #27, and #28 — build the image,
publish it to the artifact chain, deploy it to a real Proxmox VM, and
boot it there with working DHCP networking. The point of three in a row
is repeatability. The full chain — **build → publish → deploy → boot** —
runs the same way each time; this is a property of the pipeline, not a
one-off image someone hand-carried onto a hypervisor.

The distinction from QEMU matters because a real hypervisor holds the
image to a stricter standard: paravirtualized devices have to be present
in the kernel, the firmware has to find the EFI system partition the way
real UEFI does, and the network has to come up on its own. Those are
exactly the places the earlier QEMU-only path could pass while a real
target failed.

**Kconfig gotcha — virtio networking needs two symbols, not one.** On
this BSP, `CONFIG_VIRTIO_NET` alone is not sufficient to bring up virtio
networking. It also requires `CONFIG_NET_CORE`. If you are assembling a
kernel config-fragment chain for a similar target and only carry
`CONFIG_VIRTIO_NET`, the merge will look complete and the interface will
not come up. Adding `CONFIG_NET_CORE` to the fragment chain resolved it,
confirmed by a real DHCP lease on the very next build. If you build your
own config fragments for a virtio target, carry both.

**Known issue — ESP partition type on the generated `.wic`.** The GPT
partition type for the boot partition is currently emitted as `0700`
(Microsoft basic data) where a UEFI EFI System Partition should be
`EF00`. On some firmware this is benign; on others — including OVMF and
UEFI targets that key ESP detection strictly on the GPT type GUID — it
prevents the firmware from locating the ESP.

There is a working deploy-time workaround: retype the partition after
writing the image, before first boot —

```
sgdisk -t 1:ef00 /dev/sdX
```

To be clear about status: this is a workaround, not a fix. The upstream
correction — setting the ESP type in the `wic.ks` source so images are
emitted correctly — is tracked and still open; it has not landed. If you
are deploying `.wic` images to UEFI/OVMF targets today, apply the
`sgdisk` retype; do not assume the emitted image is already correct.

**What this is and isn't**: this is the Thunderhead-AMD64 canary — a
minimal console/SSH image — proven to deploy and boot on real
virtualized hardware, repeatably, with networking up. It is not a claim
about the interactive stack, which is still ahead and still in the open.
If you are building or deploying BenixOS images yourself, the pipeline
you are targeting now clears a real hypervisor, not just an emulator.
Watch [github.com/slash-builder](https://github.com/slash-builder) for
the image manifests and config fragments.
