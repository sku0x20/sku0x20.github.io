---
layout: post
title:  "android booting"
date: 2022-06-26
categories: android booting
upload-date: 2023-01-29
---

this is very basic and i might be wrong...
read with caution...
and definetly check out the additional links
ok i get it why andoid x86 is needed in the first place.
the android kernel is not the full linux kernel.
so, even if i did boot android, it won't work with the hardware.
so, x86 is like putting that stuff back into. so android works for the pc, specifically x86(64) ones...
so, even if i did boot android, audio won't work, wifi won't work, idk if even graphics will.
cause the image does not contain the required drivers for it to work...
that's why there is porting...
and there is yet to be much work done to support all the devices. like pc...
linux pc support is better but many times, things are just proprietary...
take sound issue on linux as example...
thing is i am not dropping the idea of booting android but rather not doing it rn...
it need more investment and getting those proprietary stuff, yup that means porting...
i know, it's not hard but i am not doing it rn...
maybe i will try port latest lineage os to my redmi 4a and see how that goes...
booting bare android on pc is not what i am after at least not rn.
so dropping that idea for rn...



# boot
https://unix.stackexchange.com/questions/524622/what-is-the-relation-between-uefi-and-grub


https://source.android.com/devices/bootloader


ok, so if i understood correclty,
let's take case of pc first,

so what happenes is,
with bios,
// windows
vendor(oem) -> calls mbr(i.e. executes code at specific location after it's done with it's own setup) -> windows bootloader -> windows loaded(may tale multiple steps)
// linux
vendor(oem) -> calls mbr -> grub loader(can also load windows enabling multi-boot) -> loads selected linux kernel
as you can see grub has overriden/overwritten itself as the main bootloader. oem does not know anything but just to execute 
code at a specific location.
with legacy bios, this way grub gives the ability for multi booting. which is not possible.

with the advent and uefi came into existence apart from many things,
it also gives the ability to multiboot
with uefi,
// windows
vendor(oem) -> implements uefi and calls the uefi loader-> windows implementes the structure requied by uefi(efi partition) and uefi compatable bootloader in that partition which uefi can call -> load windows
// linux
vendor(oem) -> implements uefi and calls the uefi loader -> grub uefi loader for selected linux distbution -> loads selected linux kernel
as you can see this time grub has not overriden/overwritten anything,
it's the uefi that provided the multi boot capability...
we can remove the grub altogether and replace it with any bootloader(executable efi image) that is capable of loading the linux kernel
also, linux/gnu os can give multiple options like with recovery, wihtout graphics and other stuff...
and grub knows how to load them... how? grub config set by the os, let's not go in that...

also, did is forgot that you can specify the default uefi boot target, or to open the menu everytime.
btw, these are oem sepcific settings and should be(only) available in BIOS/firmware settings.

(not fully sure on this)
now, grub being grub also gives you the option to boot other os too.
like you have three os, windows, mint and fedora.
and you set the default to mint.
since mint uses grub-efi. it can give you option to boot other os, like fedora and windows too...
so, it acts as a uefi boot manager, idk...
maybe boot manger is another concept altogether with NVRAM, fallback and stuff...

this is very basic process, and misses the details like secure boot...
it's just to give a gimplese what's different happening in case of android booting process.


now with android,
things happen a bit differently,
vendor(oem) -> it's own boot loader which loads the android kernel -> android kernel -> android loaded
the second step can be brokoen down into multiple bootloaders as per oem's wish.
it's oems responsiblity to load the android kernel with the sepcified configs...
also, it's oems responsibility to verify the integrity of the partitions.

and don't get confused by the partitions like uefi has, efi partition(esp), and linux can have /boot, /home and ever windows have a different recovery partition.
so does, android have like recovery partition, idk, system partition, etc...

ok, so why no uefi on android?
the issue is uefi is like a standard but it was tightly coupled to x86_64 platform.
but android was built for arm. so, no standardization there.
that's the reason you can't multi boot on android devices.
which is to say no live cd...

it was oems responsibilty to boot the android kernel.
however oem does it, it upto him.
so, the bootloader are proprietary...

so, what's the issue with booting android on pc?
no and issue we just need an uefi bootloader that can boot android kernel...

so, i want/need and x86 uefi compatable bootloader which can load android x86 kernel...


and is this what i need?
https://github.com/me176c-dev/android-efi

# notes

also, if i am right, uefi is mostly implemented by the cpu manufacturer
intel, qualcomm(android)

https://worthdoingbadly.com/qcomxbl/
// qualcomm old without uefi
qualcomm -> aboot(android bootloader) (modified version of  little kernel)
	        -> supports fastboot commands
		  -> loads android kernel
now if i am right, qualcomm is using uefi for booting android and they are using same
for booting windows arm64 pc too. this means can we multiboot on android devices?

tbh, now it's getting interesting and i would love to boot,
different linux distros or even windows arm version on my android device.
we will definetly do these experiments...
i will buy some old google pixel and experiment someday, espicially multiboot...


# additional links
https://www.freedesktop.org/wiki/Software/systemd/systemd-boot/
https://worthdoingbadly.com/qcomxbl/
https://github.com/android-boot-manager
https://developer.qualcomm.com/qfile/28821/lm80-p0436-1_little_kernel_boot_loader_overview.pdf
https://github.com/theopolis/uefi-firmware-parser
https://anbox.io/
https://calyxos.org/
https://docs.ubports.com/en/latest/porting/introduction/Intro.html
https://halium.org/
	- now i am understanding why it's much harder to run different os on android device. much more proprietary stuff.
	- on pc it's mostly integrated in the kernel itself. still, we get issues...
	- in case of mobile device it's more...
	- still one day, we will have, multiple distros like in pc world

https://postmarketos.org/

https://github.com/me176c-dev/me176c-boot
	- is this kind of qualcomms bootloader replacement for that particular device?? idk...
