---
layout: post
title:  "minimilistic bootloader"
date: 2022-06-10
categories: efi
upload-date: 2023-01-29
---

i want a minimilistic bootloader,
which can boot specific version of linux kernel, recovery mode and all that stuff.
but only for the os it's installed for. not all the efi os, i don't even want windows bootloader to be visible
in my bootmanager menu
but it does have an option to fix the nvram all by itself,
in that it will detect all the .efi binaries in the efi partition and make a entry for them in the nvram
so, now thing is if you want to switch os, boot different os, use the f12 or what key to load up the firmware uefi menu, 
and in that select the os you want to boot into.

also it need not have the kernel on efi partition. and should boot kernel without EFI stub set to true when compiling.
both the cases unlike systemd boot.