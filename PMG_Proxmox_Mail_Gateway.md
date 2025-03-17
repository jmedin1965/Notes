# PMG Proxmox Mail Gateway
Created Tuesday 11 March 2025

Install as a standard CT template in Proxmox. No need for a valid external domain, can just use fake internal domain.

Configure
---------

### Relaying

Configuration>>Mail Proxy>> Ports
Default Relay: foreman.jmsh-home.dtdns.net - ip or name of host to receive mail from external
Relay Port: 25 - port of above host
Disable MX lookup (SMTP): yes - we don't want to look up MX records, just use these settings instead
Smarthost: smtp.dodo.com.au:25 - our external relay

REF: <https://electrictoolbox.com/configure-postfix-external-connections/>

to get foreman.jmsh-home.dtdns.net to accept email requites;

inet_interfaces = all
or
inet_interfaces = locahost ip_address

in /etc/postfix/main.cf

but this did not work. I also had to change /etc/postfix/master.cf

127.0.0.1:smtp inet n - - - - smtpd
to
smtp inet n - - - - smtpd


### Relay Domains

Configuration>>Mail Proxy>> Ports

~~A list of all domains that we will receive and forward from external. Everything else will be rejected.~~
I have a catch-all now, so anithing sent to [user@jmsh-home.com](mailto:user@jmsh-home.com) gets relayed back out to me. Anithing going out through the PMG get the from changed as follows

[user@host.jmsh-home.com](mailto:user@host.jmsh-home.com) -> [user.host@jmsh-home.com](mailto:user.host@jmsh-home.com)

see  SMTP catch all from Greg below.

### Ports

I ended up swapping input and output ports. By default, port 25 is the external port, and 26 is the internal. Since I'm forwarding from the firewall, I created a rule to forward external port 25 to port 26 on the GW.

Configuration>>Mail Proxy>> Ports
External SMTP Port 26
External SMTP Port 25

#### Networks

A list of networks that are considered local. We will relal from everyone on this list

#### Mail Filter

#### Added Action - Has Been Scanned Notice

Just adds a message to outgoing mail. More as a test and to make sure incoming mail gets tagged.

This e-mail has been processed and 
scanned by Proxmox Mail Gateway

#### created filter - Add Disclaimer

Enabled disclaimer on outgoing mail Priority 60

#### created filter - Add Scanned Notice

Add Scanned Notice on incoming mail, Priority 60

SMTP catch all from Greg
------------------------

### main.cf

# Debian specific:  Specifying a file name will cause the first
# line of that file to be used as the name.  The Debian default
# is /etc/mailname.
#myorigin = /etc/mailname
	
smtpd_banner = $myhostname ESMTP $mail_name (Debian/GNU)
biff = no
	
# appending .domain is the MUA's job.
append_dot_mydomain = no
	
# Uncomment the next line to generate "delayed mail" warnings
#delay_warning_time = 4h
	
readme_directory = no
	
# See <http://www.postfix.org/COMPATIBILITY_README.html> -- default to 2 on
# fresh installs.
compatibility_level = 2
	
	
	
# TLS parameters
#smtpd_tls_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
#smtpd_tls_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
smtpd_tls_security_level=may
	
smtp_tls_CApath=/etc/ssl/certs
#smtp_tls_security_level=may
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
	
smtpd_relay_restrictions = permit_mynetworks permit_sasl_authenticated defer_unauth_destination
myhostname = ubeaut.work
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
myorigin = /etc/mailname
mydestination = $myhostname, ubeaut.work, localhost
relayhost = [smtp.gmail.com]:587
mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
mailbox_size_limit = 0
recipient_delimiter = +
inet_interfaces = all
inet_protocols = ipv4
	
# Enable SASL authentication
smtp_sasl_auth_enable = yes
# Disallow methods that allow anonymous authentication
smtp_sasl_security_options = noanonymous
# Location of sasl_passwd
smtp_sasl_password_maps = hash:/etc/postfix/sasl/sasl_passwd
# Enable STARTTLS encryption
smtp_tls_security_level = encrypt
# Location of CA certificates
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
	
#for receiving mail below
smtpd_tls_cert_file=/etc/postfix/ssl/smtpd.cert
smtpd_tls_key_file=/etc/postfix/ssl/smtpd.key
smtpd_use_tls=yes
smtpd_tls_session_cache_database = btree:${data_directory}/smtpd_scache
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
	
#to redirect all incoming emails
virtual_alias_maps = hash:/etc/postfix/virtual

### virtual_alias_maps = hash:/etc/postfix/virtual

@ubeaut.work [ubeaut58@gmail.com](mailto:ubeaut58@gmail.com)

pmgconfig sync --restart 1


SPF DKIM DMARC Settings
=======================

This is not easy to understand. Following i a record checker with may help

This checker is much better

<https://www.kitterman.com/spf/validate.html>

<https://dmarcly.com/tools/>

Looks like SPF records only take ip addresses, not domain names

to do
=====


* ☑ Add Postfix settings to Ansible, make sure we exclude the Proxmox mail gateway and make different settings for our internal mail receiver, added catch-all and port forward.
* ☐ postfix, Only one setting is required on all servers now in [/etc/aliases](file:///etc/aliases) root: [root@jmsh-home.com](mailto:root@jmsh-home.com). Do this with Ansible or in install.sh from scripts github



