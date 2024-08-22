# DHCP lease screen not loading
Created Thursday 22 August 2024

<https://forum.netgate.com/topic/189755/a-fix-if-you-want-to-use-system_patches-package?_=1724291002308>

Looks like at some stage, the DHCP server started adding entries to ./var/dhcpd/var/db/dhcpd.leases file for each DHCP address, weather it's being issued or not. Or else it's been like that always. The php code in /src/etc/inc/system.inc does a hostname lookup for each of these IP addresses. Either way, if you do have a number of leases in this file, the lookup takes forever, so the GUI times out. If you would like that lookup disabled and for the patch to survive an update, just do the following:


1. install the "System_Patches" package
2. navigate to System>>Patches
3. click "Add New Patch"
	1. Description: status DHCP hangs
	2. URL/Commit ID: leave blank
	3. Patch Contents: see attached file dhcp_status.patch
	4. Leave other fields as default
	5. possibly tick "Auto Apply" if you want or just apply it manually. Your choice here.
4. Now navigate to "Status>>DHCP Leases" and it loads quick, be it without the results of a dns lookup for each entry.


The other alternative is to manually add a DNS entry for each unassigned IP address in the DHCP ranges.

Hope this helps others.

