# ProxMox API
Created Monday 06 January 2025

REF: <https://pve.proxmox.com/pve-docs/pvesm.1.html>
REF: <https://pve.proxmox.com/pve-docs/api-viewer/index.html#/nodes>

list snapshots for vm 100

pvesh get nodes/host01/qemu/100/snapshot --output-format json-pretty

list config for vm 100 for snapshot test

pvesh get nodes/host01/qemu/100/snapshot/test/config --output-format json-pretty


find all snapshots REF: <https://forum.proxmox.com/threads/finding-all-snapshots-in-cluster.117478/>

