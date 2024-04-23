# Install qemu-guest-agent
Created Wednesday 11 October 2023

ref: <https://forum.netgate.com/topic/162083/pfsense-vm-on-proxmox-qemu-agent-installation>

pkg install qemu-guest-agent
(web gui): Install "Shellcmd" from the package manager "System/PackageManager"

(web gui): Create the following "earlyshellcmd" from "Service/Shellcmd":

service qemu-guest-agent start
(Shell) Edit /etc/rc.conf.local:

qemu_guest_agent_enable="YES"
qemu_guest_agent_flags="-d -v -l /var/log/qemu-ga.log"
System: Settings / Advanced: Tunables -> Add

Tunable: virtio_console_load, Value: YES
Reboot

