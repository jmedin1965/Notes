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

