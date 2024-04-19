# USB Pendrive on ProxMox VM
Created Wednesday 11 October 2023

REF: <https://forum.proxmox.com/threads/virtual-pendrive-on-vm.114143/>

Format drive as FAT32: <https://www.codenicer.com/content/formatting-usb-drive-fat32-using-freebsd>

gpart worked to get it formated

Mount USB Pen Drive: <https://docs.netgate.com/pfsense/en/latest/recipes/usb-drive-copy.html>

in the the vm's monitor screen;

drive_add 0 file=/dev/dpool03/data/vm-118-disk-1,if=none,id=drive-usb0,format=raw,cache=none
device_add usb-storage,id=drive-usb0,drive=drive-usb0,removable=on
device_del drive-usb0


