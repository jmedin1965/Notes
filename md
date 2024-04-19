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


# Dogtag - internal off-line CA
Created Friday 01 December 2023

I tried to install this using debian, but just too difficult. No point re-inventing the wheel, so I'm
installing on a CentOS 8 system with 8G disk space and an encrypted root disk.

I did find an excelent doc on how to install an off-line CA on centOS, but it's for centOS 7. I will follow
it and see if it works with centOS 8;

REF <https://github.com/rharmonson/richtech/wiki/OSVDC-Series:-Root-Certificate-Authority-(PKI)-with-Dogtag-10.3-on-CentOS-7.3.1611>

install apache and php - NOT REQUIRED!
--------------------------------------

centOS 8 uses tomcat. If these are installed, dogtag instalation fails

Configure firewall
------------------

CentOS 8 used firewall-cmd

firewall-cmd --permanent --zone=public --add-port=8443/tcp
firewall-cmd --permanent --zone=public --remove-service=cockpit

firewall-cmd --reload 
firewall-cmd --list-all

Entropy
-------

make sure quemu-guest-client is installed. Also add VirtIO RNG to the guest hardware

yum install qemu-guest-agent

Make sure you have the VirtIO RING added to vm's hardware and reboot

selinux
-------

need to set selinux to permissive to do the install, or probably not? I'm thinking that CentOS8 will
hopefullt set selinux up properly. yes they did.

# no need for this
#sudo setenforce 0
#sudo sed -i 's/^SELINUX=.*/SELINUX=permissive/g' /etc/selinux/config

hosts file
----------

make sure hosts ip address in in /etc/hosts file

<ip address> <hostname.domain> <hostname>

389-ds-base pki-ca
------------------

REF: <https://www.techsupportpk.com/2020/04/how-to-set-up-389-directory-server-centos-rhel-8.html>

yum -y install @idm:DL1
yum -y install 389-ds-base pki-ca

unistall cockpit if you don't want to use it

dscreate interactive

systemctl enable dirsrv.target
systemctl enable [dirsrv@ca.service](mailto:dirsrv@ca.service)
systemctl start dirsrv.target
systemctl start [dirsrv@ca.service](mailto:dirsrv@ca.service)
systemctl status dirsrv.target
systemctl status [dirsrv@ca.service](mailto:dirsrv@ca.service)
lsof -i -P -n | grep LISTEN

dogtag theme
------------

get the version ok pki that is installed

yum list installed  | grep pki-base.noarch

during this install the version was 10.8.3 and this is the one that I found

yum install <http://rpmfind.net/linux/fedora/linux/releases/32/Everything/x86_64/os/Packages/d/dogtag-pki-server-theme-10.8.3-1.fc32.noarch.rpm>

Setup Dogtag CA
---------------

pkispawn -s CA

if it fails and you need to remove, use
pkidestroy -s CA

systemctl enable pki-tomcatd.target
systemctl enable pki-tomcatd@
systemctl start pki-tomcatd.target
systemctl start pki-tomcatd@

now access via

<https://dogtag01.gli.lan:8443/ca>
    
==========================================================================
INSTALLATION SUMMARY
==========================================================================

Administrator's username:             caadmin
Administrator's PKCS #12 file:
/root/.dogtag/pki-tomcat/ca_admin_cert.p12

To check the status of the subsystem:
systemctl status [pki-tomcatd@pki-tomcat.service](mailto:pki-tomcatd@pki-tomcat.service)

To restart the subsystem:
systemctl restart [pki-tomcatd@pki-tomcat.service](mailto:pki-tomcatd@pki-tomcat.service)

The URL for the subsystem is:
<https://dogtag01.gli.lan:8443/ca>

PKI instances will be enabled upon system boot

==========================================================================

looks like I've somehow got it working to this point
----------------------------------------------------

This is a youtube tutorial

<https://www.youtube.com/watch?v=-Fak3EdUiOE>

The instructions ara a little dated, but they can be followed. I generated the signing request using openssl
then got dogtag to sign it.

# FreeIPA with off-line CA
Created Friday 01 December 2023

I've installed a standaline dogtag server to use an an off-line CA

I'm going to install FreeIPA on a proxmox LXC container and sign it's certificate with the dogtag CA
server. The lxc container will be unpriviliged.

After a CentOS 8 install from template, I had to install some extra packages;

yum install sudo

REF <https://floblanc.wordpress.com/2016/09/02/using-a-dogtag-instance-as-external-ca-for-free-ipa-installation/>

Install part 1
--------------

replace the following below with your stuff;

--hostname=
-n your domani
-r your domain un upper case
-p a super secret password
-a a super secret password

no need for --forwarder if you have /etc/resolve.conf, as this is filled with these values

ipa-server-install \
--hostname=fq.host \
--setup-dns \
--no-ntp \
--setup-adtrust \
--setup-kra \
-n domain \
-r DOMAIN \
--netbios-name=GLI \
-p 'password' -a 'password' \
--external-ca

Install part 2
--------------

now cat /root/ipa.csr and copy

got to dogtag web, SSL End Users Services, Manual Certificate Manager Signing Certificate Enrollment.

Configure firewallste copied certificate and fill in your information

take note of request number

go back to, Agent Services, List Requests

find the certificate request and click it, and approve it

now go back and click list certificates and find it, as the approval page does not have the complete certificate

copy the base64 encoded part to /root/ipa.cert

go back to list certificates and click on the CA, which would be certificate 1

copy the base64 encoding part tp /root/dogtagca.cert

now run part 2

/sbin/ipa-server-install --external-cert-file=/root/ipa.cert --external-cert-file=/root/dogtagca.cert

enter your super secret password

sucess message
--------------

The ipa-client-install command was successful

==============================================================================
Setup complete

Next steps:

1. You must make sure these network ports are open:

TCP Ports:
  * 80, 443: HTTP/HTTPS
  * 389, 636: LDAP/LDAPS
  * 88, 464: kerberos
  * 53: bind
UDP Ports:
  * 88, 464: kerberos
  * 53: bind


2. You can now obtain a kerberos ticket using the command: 'kinit admin'

   This ticket will allow you to use the IPA tools (e.g., ipa user-add)
   and the web user interface.

3. Kerberos requires time synchronization between clients

   and servers for correct operation. You should consider enabling chronyd.

Be sure to back up the CA certificates stored in /root/cacert.p12
These files are required to create replicas. The password for these
files is the Directory Manager password
The ipa-server-install command was successful

# Ipfire firewall and router
Created Friday 01 December 2023

my local packages
-----------------

How do I inject my own package into ipfire so I can install and remove it easily?
I'm going to generate a pgp key, then create a package file and then enable this key to be recognised.
The package still needs to be coped manualy though. I'm thinking a pakfire-local wrapper for pakfire

### generate gpg key

REF: <https://www.golinuxcloud.com/tutorial-encrypt-decrypt-sign-file-gpg-key-linux/>

set the GPG home folder
export GNUPGHOME=/opt/pakfire/etc/.gnupg

generate the key
gpg --gen-key

export key and secure key to store in a safe place
gpg --list-keys
gpg --export --armor --output public.asc <fingerprint frim --list-keys above>
gpg gpg --export-secret-keys --armor --output secret.asc <fingerprint frim --list-keys above>

now we can sign a tar file package. have to use -r option to work with ipfire. This creates a .gpg file
gpg -R --encrypt --sign -r "key name or fingerprint" <package file>

then we can veryfy the .gpg file just like pakfire does and we can read the fingerprint, just after the
VALIDSIG statement
gpg --verify --status-fd 1 <package file>

### now to hack a wrapper for pakfile


    
    

to do
-----

### ipfire /var/ipfire/failover/vrrp.notify.sh permissions

keepalived complains about elevated rights for this script. Need to give it rights to create;

/var/ipfire/red/active
/var/run/keepalived.state

as the web user, which is nobody

# PFSense
Created Thursday 21 September 2023


# Firewall aliases explained
Created Wednesday 11 October 2023

This YouTube explains how to use Aliases

REF: <https://www.youtube.com/watch?v=xlx2REV3-wo>

# Format disk drive as MSDosFS
Created Thursday 12 October 2023

/bin/dd if=/dev/zero of="$1" count=10

(
echo n
echo y
		
# sysID 11 is MSDos
echo 11
		
echo
echo
echo n
echo y
echo n
echo n
echo n
echo n
echo y

) | /sbin/fdisk -u "$1"

/sbin/fdisk "$1"

#F16 is Fat16, can use F32 but on small disk it doesn't work
/sbin/newfs_msdos -F16 "${1}s1"

/sbin/mount -t msdosfs "${1}s1" $mount

# HAProxy keep alive for ssh
Created Wednesday 11 October 2023

-  timeout client 50000ms
-  timeout server 50000ms
-  retries 50
+  timeout client 3000ms
+  timeout server 3000ms
+  retries 1

After rolling it back it started to work perfectly again, but we still weren’t 100% sure why. Using the shorter timeout, the channel was reconnecting every 3 seconds and some requests failed to receive the response, but why it wasn’t the case for the 50 seconds timeout?

Cause
In order to keep the WebSocket connection alive, Phoenix Socket library sends a heartbeat message which happens every 30 seconds by default. With 50 seconds timeout for the client/server everything worked fine, reducing the timeout to merely 3 seconds made WebSocket connection to timeout, so that when request took any longer, the frontend was unable to receive the response on time.

Solution
Obvious one was to reduce the heartbeat interval to something less than 3 seconds to make sure we keep the connection alive even with the migration config, but it wasn’t really appealing to spam the server such often.

After some googling we figured out there was another HAProxy timeout setting which is responsible for a tunnel connections:

The tunnel timeout applies when a bidirectional connection is established
between a client and a server, and the connection remains inactive in both
directions
<https://cbonte.github.io/haproxy-dconv/1.7/configuration.html#4-timeout%20tunnel>

When connection becomes a tunnel (as it happens for WebSockets) this timeout setting supersedes both the client and server timeouts.

Here’s how it looks in the config:

timeout client 3000ms
timeout server 3000ms
timeout tunnel 50000ms

REF: <https://lucjan.medium.com/investigating-websocket-haproxy-reconnecting-and-timeouts-6d19cc0002a1>

# install and use bind instead of Unbound
Created Thursday 12 October 2023

REF: <https://geekistheway.com/2023/03/18/configuring-dns-bind9-on-your-pfsense/>

Configuring DNS Bind9 on your pfSense
-------------------------------------
Posted on March 18, 2023 by Thiago Crepaldi
Last Updated on March 18, 2023 by Thiago Crepaldi

If there is one annoying thing on pfSense that seems to be never fixed is its DNS Resolver service called Unbound. Release after release, the Netgate folks still struggle to identify and fix the random crashes, unexpected restarts and whatnot. In this post, we are going to install Bind9, a very solid DNS server, to replace Unbound.

**__pfBlockerNG depends on Unbound, so don’t replace it with Bind if you still want to block stuff with it.__**

On our setup, we are going forward recursive queries to external name servers, such as 1.1.1.1 and 1.0.0.1. The internal names/IPs will be resolved by bind through zone records of our own. For this example, I am going to assume there is a webserver.lan.example.com that must resolve to 192.168.0.100.

To get started, first access your pfSense using its IP instead of the FQDN. That is because we are going to disable the DNS Resolver before we can enable Bind. Next, go to **System >> Package Manager >> Available Packages**, find bind in the list and click on **Install**.

Once installation finishes, go to **Service >> BIND DNS** Server and do as follows:


* Settings
	* Daemon settings:
		* Enable BIND: Checked
		* IP Version: IPv4
		* Listen On: LAN or whatever other interface you want to serve as DNS server
		* Enable Notify: Unchecked
		* Hide Version: Checked
		* Limit Memory Use: 256MB
	* Logging options:
		* Enable logging: Checked
		* Logging Severity: Warning
		* Logging Options: Default
	* Response Rate Limit:
		* Rate Limit: Checked
		* Limit action: Deny query
		* limit: 15
	* Forwarder Configuration:
		* Enable Forwarding: Checked
		* DNSSEC Validation: On
		* Forwarder IPs: 1.1.1.1;1.0.0.1
	* Advanced Features:
		* Listen port: 53
		* Control port: 8953
		* Custom Options: leave blank
		* Global Settings: leave blank
	* Click on Save
* ACLs
	* Click on Add
		* General Options:
			* ACL Name: Trusted-ACL
			* Description: ACL for trusted clients
			* Enter IP or network range block: 192.168.0.0/24 or whatever range you use
			* Click on Save
* Views
	* Click on Add
		* General Options:
			* View name: Trusted-View
			* Description: Trusted IPs allowed to perform recursive DNS queries
			* Recursion: Yes
			* match-clients: Trusted-ACL
			* allow-recursion: Trusted-ACL
		* Custom Views:
			* Custom Options: leave blank
	* Click on Save
* Zones
	* We need two zones, the first one is a Forward zone. Click on Add
		* Domain Zone Configuration
			* Disable this zone: Unchecked
			* Zone name: lan.example.com or whatever FQDN you use on your network
			* Description: Trusted Forward zone for lan.example.com
			* Zone Type: Master
			* View: Trusted-View
			* Reverse zone: Unchecked
			* IPv6 Reverse Zone: Unchecked
			* Response Policy Zone: Unchecked
			* Custom option: leave blank
		* DNSSEC
			* Inline signing: Unchecked
		* Master Zone Configuration
			* TTL: 86400
			* Nameserver: localhost
			* Base domain IP: 127.0.0.1
			* Mail Admin Zone: root@localhost or a real email
			* Serial: 1 or something like YYYYMMDDxx (e.g. 2023031701)
			* Refresh: 1d
			* Retry: 2h
			* Expire: 4w
			* Minimum: 1h
			* allow-update: None
			* Enable update-policy: Unchecked
			* allow-query: Trusted-ACL
			* allow-transfer: None
		* Zone Domain Records:
			* Add an entry
				* Record: webserver
				* Type: A
				* Priority: leave blank
				* Alias or IP address: 192.168.0.100
			* Register DHCP Static Mappings: Checked
		* Custom Zone Domain Records:
			* leave empty
	* Add a second zone that will be you reverse zone
		* Domain Zone Configuration
			* Disable this zone: Unchecked
			* Zone name: 0.168.192.in-addr.arpa whatever network range you use on your network. Just remember it must be typed in reverse order and only the static blocks are typed.
			* Description: Trusted Reverse zone for lan.example.com
			* Zone Type: Master
			* View: Trusted-View
			* Reverse zone: Checked
			* IPv6 Reverse Zone: Unchecked
			* Response Policy Zone: Unchecked
			* Custom option: leave blank
		* DNSSEC
			* Inline signing: Unchecked
		* Master Zone Configuration
			* TTL: 86400
			* Nameserver: localhost
			* Base domain IP: 127.0.0.1
			* Mail Admin Zone: root@localhost or a real email
			* Serial: 1 or something like YYYYMMDDxx (e.g. 2023031701)
			* Refresh: 1d
			* Retry: 2h
			* Expire: 4w
			* Minimum: 1h
			* allow-update: None
			* Enable update-policy: Unchecked
			* allow-query: Trusted-ACL
			* allow-transfer: None
		* Zone Domain Records:
			* Add an entry
				* Record: 1
				* Type: PTR
				* Priority: leave blank
				* Alias or IP address: webserver.lan.example.com
			* Register DHCP Static Mappings: Checked
		* Custom Zone Domain Records:
			* leave empty


Click on **Save** and **Apply settings** as usual.

You can test your settings by trying to resolve internal and external names and IPs using dig. Below is an example on how to test external name resolution:

	$ dig disney.com
	
	; <<>> DiG 9.16.1-Ubuntu <<>> disney.com
	;; global options: +cmd
	;; Got answer:
	;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 56230
	;; flags: qr rd ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0
	;; WARNING: recursion requested but not available
	
	;; QUESTION SECTION:
	;disney.com.                    IN      A
	
	;; ANSWER SECTION:
	disney.com.             0       IN      A       130.211.198.204
	
	;; Query time: 220 msec
	;; SERVER: 172.18.240.1#53(172.18.240.1)
	;; WHEN: Fri Mar 17 23:51:17 EDT 2023
	;; MSG SIZE  rcvd: 54

In order to resolve the reverse IP mapping of an external server, run:

	$ dig -x 1.1.1.1
	
	; <<>> DiG 9.16.1-Ubuntu <<>> -x 1.1.1.1
	;; global options: +cmd
	;; Got answer:
	;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 26423
	;; flags: qr rd ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0
	;; WARNING: recursion requested but not available
	
	;; QUESTION SECTION:
	;1.1.1.1.in-addr.arpa.          IN      PTR
	
	;; ANSWER SECTION:
	1.1.1.1.in-addr.arpa.   0       IN      PTR     one.one.one.one.
	
	;; Query time: 20 msec
	;; SERVER: 172.18.240.1#53(172.18.240.1)
	;; WHEN: Fri Mar 17 23:53:29 EDT 2023
	;; MSG SIZE  rcvd: 87

I hope this helps, have fun!

Bind on backup server
---------------------

This sort of works well in a way. Looks like the dynamic zone files are cleared on every reboot though. So not sure if this will worrk well as a master-slave setup. Probably the best way for this to work is all slaves and the master is on a different system such as a Samba 4 DC.

on the sync tab, just add the IP address of all the backup PFSense servers along with the username and password the same as the HA setup. The config files wi l then sync across. Make sure you enable the DNS TCP/UPD port 53 on the sync network too.

just a test line  

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


# Postfix configuration for a host/server
Created Friday 01 December 2023

I'm using postfix to send out emails from all the vm's that need to send emails out. It's pre-installed most of the time. If not just do an

apt install postfix

To send out emails, if the hosts domain is not real, then you need to change myorigin to a valid domain and also use the bse-mailx package instead of mailutils. If you do use mail from mailutils, then use the -r option. If you don't, the from field of your outgoing messages will be invalid, which will cause the message to be rejected.

send options
------------

mydomain = servers domain

myhostname = fully qualified name

myorigin = This is important if your domain 
is not a real domain because outgoing get stamped using
this as the from address. By default on ubuntu, this is set to a file

mydestination = a list of command separated fully qualifies server names. All
of these are considered to be localy handled by this server, and are delivered
localy.

relayhost = This is the host that will reay outgoing mail for us.

mynetworks = space separated list of local networks





Install and a standars CT template in proxmox. No need for a valid external domain, can just use fake internal domain.

Configure
---------

### Relaying

Configuration>>Mail Poxy>> Ports
Default Relay: forenam.jmsh-home.dtdns.net - ip or name of host to receive mail from external
Relay Port: 25 - port of above host
Disable MX lookup (SMTP): yes - we don't want to look up MX records, just use these settings instead
Smarthost: smtp.dodo.com.au:25 - our external relay

REF: <https://electrictoolbox.com/configure-postfix-external-connections/>

to get forenam.jmsh-home.dtdns.net to accept email requites;

inet_interfaces = all
or
inet_interfaces = locahost ip_address

in /etc/postfix/main.cf

but this did not work. I also had to change /etc/postfix/master.cf

127.0.0.1:smtp inet n - - - - smtpd
to
smtp inet n - - - - smtpd


### Relay Domains

Configuration>>Mail Poxy>> Ports

A list of all domains tha we will receive and forward from external. Everithing else will be rejected.

### Ports

I ended up swapping input and output ports. By default, port 25 is the external port, and 26 is the internal. Since I'm forwarding from the firewall, I created a rule to forward external port 25 to port 26 on the GW.

Configuration>>Mail Poxy>> Ports
External SMTP Port 26
External SMTP Port 25

### Networks

A list of networks that are considered local. We will relal from everyone on this list

#### Mail Filter

#### Added Action - Has Been Scanned Notice

Just adds a message to outgoing mail. More as a test and to make sure incoming mail gets tagged.

This e-mail has been processed and 
scanned by ProxMox Mail Gateway

#### created filter - Add Disclaimer

Enabled disclaimer on outgoing mail Priority 60

#### created filter - Add Scanned Notice

Add Scanned Notice on incomming mail, Priority 60

to do
=====

ansible
=======

add the above settings to ansible

make sure we exclude the proxmox mail gateway and make different settings for our intermal mail receiver

# ProxMox
Created Thursday 18 April 2024

A repo with proxmox helper scripts thet Greg sent.

<https://tteck.github.io/Proxmox/>

# ProxMox Mail Gateway
Created Friday 01 December 2023

Used to relay and receive email in and out. Easy to manage and configure once you get your head around it.

install
-------

Install as a standars CT template in proxmox. If you want to send emails out from it like reports, it will need a valid domain, as the "myorigin" in /etc/postfix/main.cf  can't be changes as far as I can tell.

Configure
---------

### Relaying

Configuration>>Mail Poxy>> Ports
Default Relay: forenam.jmsh-home.dtdns.net - ip or name of host to receive mail from external
Relay Port: 25 - port of above host
Disable MX lookup (SMTP): yes - we don't want to look up MX records, just use these settings instead
Smarthost: smtp.dodo.com.au:25 - our external relay

REF: <https://electrictoolbox.com/configure-postfix-external-connections/>

to get forenam.jmsh-home.dtdns.net to accept email requites;

inet_interfaces = all
or
inet_interfaces = locahost ip_address

in /etc/postfix/main.cf

but this did not work. I also had to change /etc/postfix/master.cf

127.0.0.1:smtp inet n - - - - smtpd
to
smtp inet n - - - - smtpd


### Relay Domains

Configuration>>Mail Poxy>> Ports

A list of all domains tha we will receive and forward from external. Everithing else will be rejected.

### Ports

I ended up swapping input and output ports. By default, port 25 is the external port, and 26 is the internal. Since I'm forwarding from the firewall, I created a rule to forward external port 25 to port 26 on the GW.

Configuration>>Mail Poxy>> Ports
External SMTP Port 26
External SMTP Port 25

### Networks

A list of networks that are considered local. We will relal from everyone on this list

### Mail Filter

#### Added Action - Has Been Scanned Notice

Just adds a message to outgoing mail. More as a test and to make sure incoming mail gets tagged.

This e-mail has been processed and 
scanned by ProxMox Mail Gateway

#### created filter - Add Disclaimer

Enabled disclaimer on outgoing mail Priority 60

#### created filter - Add Scanned Notice

Add Scanned Notice on incomming mail, Priority 60

# Proxmox on DL360p Gen8
Created Friday 07 October 2022

The idea is to install proxmox on a DL360p Gen8. The problems are;

* The server is PCIe spot challenged. It only has 2 slots. a 16x and an 8x spot.
* It also has a built in slot with a 4x1G card.
* the built-in raid card Smart Array P420i that can't be flashed to IT mode
	* The raid array card can be switched to HBA mode but there are some warnings, so just don't know if this will work or not.
	* It does not work. Using RAID6 with LVM2 and this works much faster and does not need a CACHE module either.
* The caddy LED doesn't appear to work in HBA mode. It's a know limitation in HBA mode.
* The server does not support UEFI boot, only BIOS.
* Proxmox with Open Media Vault OVM installed on top works great.


Hardware required
-----------------

* 8 x 4TB seagate drives
	* Drives bought from Officeworks; 4TB expansion Partable Drive
	* $134 each
* 8 x disk caddies for the 8 drives above
	* <https://www.ebay.com.au/itm/152994582000>
	* 651687-001 HP Gen8 Gen9 2.5in SFF HDD SSD Drive Caddy Tray for DL180 DL380 G8 G9
	* $17.79 each
* 1 x 2 port 10G card to replace the 4x1G card
	* ~~<https://www.ebay.com.au/itm/192513997944> - this one did not work~~
		* ~~$30.57~~
	* <https://www.ebay.com.au/itm/185607289358> - This one worked perfectly
		* 647581-B21 HP Ethernet 10Gb 2P 530FLR-SFP+ ADAPTER 649869-001 647579-001
		* $24.00
* ~~1 x Sandisk 32G USB 2.0 pen drive~~
	* ~~<https://www.officeworks.com.au/shop/officeworks/p/sandisk-cruzer-blade-usb-flash-32gb-3-pack-sdcz5032p3>~~
	* ~~$18.59~~
* ~~1 x SATA to USB cabe (only needed to make USB pen drive bootable, or to recover the system)~~
	* ~~<https://www.ebay.com.au/itm/132712657279>~~
	* ~~$7.46~~


Prep work
---------

### labels

stick labels on all the disk caddies so that when a disk fails, you know which one it is.

Found out the hard way that disk/by-id does not user the serial number but the WWN, which stands for World Wide Name. Make sure your lable has this number instead of serial number.

### Server fans too loud

If you have non HP hardware installed, then ilo software will run the fans loud. It's a known problem. There is a firmware hack that will let you tweak the temperature settings to slow down the fans to a normal speed. This YouTube explains it really well. It works but don't stuff the firmware upgrade up, as there is not coming back from a bricked ilo4.

#### YouTube tutorial and how to

shows how to set fan speed and power supply fans
REF: <https://www.reddit.com/r/homelab/comments/di3vrk/comment/firx6op/>

<https://www.youtube.com/watch?v=Keyz-9HNr7Q>

#### how to flash firmware
Tried to flash but have to switch ilo protection off to flash it.


1. [Download iLO4 2.50](https://support.hpe.com/hpsc/swd/public/detail?swItemId=MTX_42ef22e4dff6423e8dbe111904) CP027911.scexe We'll use this for flashing the hacked firmware
2. Download the custom 2.73 ROM We'll swap out the original firmware in the 2.50 iLO4.
3. Disable iLO security by way of the system maintenance switch on your motherboard, which is "switch 1 to on" REF: [System maintenance switch](https://techlibrary.hpe.com/docs/iss/DL380pGen8/setup_install/advanced/Content/137614.htm)
4. For Ubuntu, I had to do the following:
	1. sudo modprobe -r hpilo
5. Replace the 2.50 ROM with the [2.77 ROM](https://www.reddit.com/r/homelab/comments/sx3ldo/hp_ilo4_v277_unlocked_access_to_fan_controls/) and flash

sh ./CP027911.scexe --unpack=ilo_250
cd ilo_250
cp /path/to/ilo4_277.bin.fancommands ilo4_250.bin
sudo ./flash_ilo4 --direct
	
and now remote console works with HTML 5, which is great !!!!!!

#### Configuration  fan commands
We want to aim for 39%, which is normal for a standard server

ssh to ilo. If no display, reset ilo with "information>>diagnostics>>reset".

fan info g

look for fan number with a star. It is the one to the left.

fan info a

for me it was the set point (sp) for sensor 11. It was set to 46deg (4600) I slowly raised it to 54 (5400) and it settled on speed of 90

fan pid 11 sp 5400

fan pid 11 sp 5900
fan pid 42 lo 10000
fan pid 40 sp 4700
fan pid 46 sp 4400
fan pid 50 sp 3800
fan pid 31 sp 5300

The youtube said to disable it, but there was a comment about setting the target temperature.

And now I have 39% fan speed.

#### ilo connection timeout
ilo connection timeout can be set to infinite at Administration>> Access Settings>>Idle connection timeout (minutes)>>infinite
I disabled serial comunication timeout too, to see if this will stop loosing session display loss.

#### Scripts to update fans from ssh

REF: <https://github.com/That-Guy-Jack/HP-ILO-Fan-Control>

### The disk drives are slow in HBA mode

Need to test this. I have 5 Raidz2 disks. They work ok but have freeze hiccups. I will re create with 8 drives and test. If this fails, I will switch off HBA and install normal RAID6 and just put zfs on to of that and test this way, or just dump ZFS all together, but I'm used to using it now.

update: I just did a dd test on zfs and then one on a single drive formatted as fat32. Just a single drive is way faster that raidz2 on 5 drives. I'm deffinitely going to test with just raid6. Also, it looks like ovm does work installed on top of proxmox and the zfs module works too. Just not with zfs on the rootfs. It causes problems. So will re-install proxmox using ext4 on the raid6 array. This will make it bootable again and the disk LED's will work too. I will use a small partition and then install zfs on the rest of the disk. Probably 256G as root and 256 of swap and the rest zfs.

update: removed HBA mode, created RAID6 with the 8 drives. Now we do not need a USB drive to boot and LVM2 is much faster that ZFS by a factor of 4. LVM2 is the way to go.

### Ethernet 10Gb 2-port 530FLR-SFP+ Adapter
<https://pci-ids.ucw.cz/read/PC/14e4/168e>

 Vendor 14e4 -> Device 14e4:168e

1014 0492	PCIe2 2-port 10 GbE BaseT RJ45 Adapter (FC EN0W; CCIN 2CC4)	
103c 1798	Flex-10 10Gb 2-port 530FLB Adapter [Meru]	
103c 17a5	Flex-10 10Gb 2-port 530M Adapter	
103c 18d3	Ethernet 10Gb 2-port 530T Adapter	
103c 1930	FlexFabric 10Gb 2-port 534FLR-SFP+ Adapter	
103c 1931	StoreFabric CN1100R Dual Port Converged Network Adapter	
103c 1932	FlexFabric 10Gb 2-port 534FLB Adapter	
103c 1933	FlexFabric 10Gb 2-port 534M Adapter	
103c 193a	FlexFabric 10Gb 2-port 533FLR-T Adapter	
103c 3382	Ethernet 10Gb 2-port 530FLR-SFP+ Adapter	
103c 339d	Ethernet 10Gb 2-port 530SFP+ Adapter	
193d 1003	530F-B	
193d 1006	530F-L	
193d 100f	NIC-ETH522i-Mb-2x10G

No luck with this card but I think it's just a faulty card.

Switch raid controller to hba
-----------------------------

### The boot disk to do the conversion
I used ubuntu-22.04.1-desktop-amd64.iso on a ventoy boot USB. I have the iso boot a persistent.

REF <https://www.ventoy.net/en/plugin_persistence.html#:~:text=Many distros (like Ubuntu%2FMX,time you boot to it>.

There is a trick to creating the persistent dat/bin file though so read the instructions from the

 
URL above "Backend Image File".

edit or create \ventoy\ventoy.json

"persistence": [
{
"image": "/ISO/ubuntu/ubuntu-20.04-desktop-amd64.iso",
"backend": [
"/persistence/ubuntu_20.04_1.dat"
],
"autosel": 1,
"timeout": 20
},
{
"image": "/ISO/ubuntu/ubuntu-22.04.1-desktop-amd64.iso",
"backend": [
"/persistence/ubuntu_22.04_1.dat"
],
"autosel": 1
}
]
}

### The conversion
REF: <https://forums.unraid.net/topic/82007-solved-unraid-with-hp-p420i-raid-card-in-hp-proliant-dl380p-g8/>

* make bootable persistent ubuntu install, see above using ventoy
* boot into ubuntu
* download hpssacli-2.10-14.0_amd64.deb from <https://downloads.linux.hpe.com/SDR/repo/mcp/pool/non-free/>
* dpkg --install hpssacli-2.10-14.0_amd64.deb
* cd [/opt/hp/hpssacli/bld](file:///opt/hp/hpssacli/bld)
* [./hpssacli](./md_files/ProxMox/Proxmox_on_DL360p_Gen8/hpssacli)
* ![](./md_files/ProxMox/Proxmox_on_DL360p_Gen8/pasted_image001.png)


Installation
------------
Below is how to install proxmox with controller in HBA mode. This requires a USB drive to keep the boot partition. Using the disks in RAID 6 and installing LVM2 menas you don't need a USB stick. The disk array is now bootable and much fastre.

Since only USB is bootable and not the HBA disk drives. I am going to create a RAIDZ2 array with the first 6 disks. I plan to leave the other 2 for TrueNas. I will limit the ROOT rpool to 128G, which will give rpool of 512G. I will also add another partition to each of the 8 drives for swap.

So, about swap. I have read a lot about not having swap and that RAM is so cheap, why should you slow down your server by having slow swap;

* server RAM is not cheap
* setting swappiness in thge kernet to 0 does not disable swap, like it's mentioned on the internets. These people have no clue.
* In the past, as a rule of thump, for a running app, 80% of the time is spent in 20% of the code. Now, this will not be the case now because app now use a lot more data. But even if this is 80% of the time is spen in 50% of the code then, you are wasting that 50% of ram used. The Linux kernel is written very efficiently. It uses swap efficiently. During startup and application initialises a lot and then never looks at this again. Why have this code sitting in valuable RAM. Why not lt the efficient kernel just swap it to disk where it will never be called for again.


### Install proxmox

* again using ventoy boot the proxmox iso which is proxmox-ve_7.2-1.iso for me now.
* You may need to tweak the BIOS to be able to boot from USB. I set external USA has priority so that ventoy USB will boot if installed. If not the 32G internal thumb drive will boot. Yes, the server has one internal USB. All the USB ports are version 2.0.
* unselect the internal USB and external USB and choose the first 6 4TB scsi drives.
* For the format choose ZFS RAIDZ2. This lets you loose 2 disks and still have a working system
* click on advanced and set the size to 128G
* now install and set the system name and email address and IP address.
* the system won't boot, just power it off now
	* pop out one of the disk caddies
	* connect the sata to USB to it.
	* plug it in to where the vertoy boot disk was
	* boot the system. It should boot to the USB HDD as normal now.
	* Follow the instructions for replacing a failed boot disk, but we are not replacing one. We are just adding one
		* REF:  <https://pve.proxmox.com/pve-docs/chapter-sysadmin.html#_zfs_administration>
			* Changing a failed bootable device
			* "proxmox-boot-tool status" should show all your boot disks (6 in total)
			* "cat [/proc/partitions"](file:///proc/partitions%22) this will list all partitions so you can work out which is the USB pen drive. You should only have one installed of size 32G. All others will be 4TB.
			* "sgdisk <healthy bootable device> -R <new device>"
				* example, but don't use this, work out what the device names should be for you "sgdisk /dev/sda -R [/dev/sdg"](file:///dev/sdg%22)
			* "sgdisk -G <new device>" don't forget this step. I did once and had bad things happen since two of my boot partitions had the same UUID.
			* so, you will get complaints about partition3 since it just doesn't fit. Ignore this since we will delete it
			* "fdisk [/dev/sdg"](file:///dev/sdg%22) please use your device and don't jusy copyu the example here
				* d 3
				* M
				* p
					* make sure the partition has a * under boot. If not press "a", which toggles the bootable flag. I had to do this to make the USB boot. Funny, it's not just for proxmox. Other people complain about truenas on a USB peodrive install having the same problem. But if the USB device is a HDD, it boots without it. Ok, and it only affects BIOS boot, not UEFI.
				* w
				* q
			* "proxmox-boot-tool format <new disk's ESP> --force" 
				* i had to use --force to get it to format.
				* this partition is partition2, so as an example "proxmox-boot-tool format /dev/sdg2 --force
			* proxmox-boot-tool init <new disk's ESP>
			* update-grub
				* I think it runs this anyway automagically but it can't hurs.It takes a while because it does it on all 7 drives. This is run every time grub is updated, so when you do alt update/upgrade.
				* oh, ok, it just runs "proxmox-boot-tool refresh", so just run that then.
		* so now the internal USB should be bootable so if you reboot it should boot from the internal USB.
		* "poweroff" the server
		* put back the 4TB caddy
		* power the server back on
		* if it boots then great. things to check;
			* cat [/etc/kernel/proxmox-boot-uuids](file:///etc/kernel/proxmox-boot-uuids)
				* this will list the UUID of all your boot disks. You should have 7. 6 disks and 1 USB pen drive
			* ls -l [/dev/disk/by-uuid](file:///dev/disk/by-uuid)
				* this will list the UUID if all disks. Plese compare. If they don't match up. You can add the missing one. [/etc/kernel/proxmox-boot-uuids](file:///etc/kernel/proxmox-boot-uuids) is just a text file.
				* proxmox-boot-tool has lots of options; refresh, kernel list, status
		* tidy up steps (best you learn how to manage zfs now so you don't break a production system). Oh and best to turn on zfs autocomplete for bash
			* run "zpool status" and make all the raidz2 disks start with scsi-. the disk I user to boot from swapped to using sde3 which is bad. You get a warning when the server boots. It is easily repairable.
				* ls -l [/dev/disk/by-id/scsi-*](file:///dev/disk/by-id/scsi-%2A)
				* find the one that matched the device and get the scsi- name instead.



Post install stuff
------------------

### DMAR: DRHD: handling fault status reg 2

Error on console log after boot. 

REF: <https://bugzilla.kernel.org/show_bug.cgi?id=202723>

Fix by doing the following, add intel_iommu=igfx_off to kernel command line

### NVIDIA GPU Driver

on a root terminal;

apt install nvidia-driver
ls [/dev/nvidia](file:///dev/nvidia)


# readme
Created Friday 19 April 2024

My Notes created using [Zim.](https://zim-wiki.org/)

Documentation is published using zim to html using a git hook.

Documentation is here [docs](https://jmedin1965.github.io/Notes/index.html)

Github code is here [code](https://github.com/jmedin1965/Notes)

# wsl
Created Thursday 21 September 2023

exec from cmd in the background
-------------------------------

C:\Windows\System32\wsl.exe --shell-type login /bin/bash -i -c "/usr/bin/zim&"

