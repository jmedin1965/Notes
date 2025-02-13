# debian boot fix BIOS EFI
Created Friday 20 October 2023

EFI Shell Cheat Sheet
---------------------

*****

<https://mricher.fr/post/boot-from-an-efi-shell/>

Debian swap BIOS to EFI boot how to
-----------------------------------

*****

<https://blog.getreu.net/projects/legacy-to-uefi-boot/>

don't forget to do the following;

mkdir [/boot/efi/EFI/BOOT](file:///boot/efi/EFI/BOOT)
cp /boot/efi/EFI/debian/grubx64.efi [/boot/efi/EFI/BOOT/BOOTX64.EFI](file:///boot/efi/EFI/BOOT/BOOTX64.EFI)

otherwise you will be driven crazy with not being able to boot


