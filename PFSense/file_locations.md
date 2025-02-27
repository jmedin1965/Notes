# file locations
Created Tuesday 27 August 2024

BIND DNS
--------

/usr/local/pkg/bind.xml

DHCP server
-----------


Services auto source
====================

Looks like all services as they load source these files or folders

[/etc/rc.conf](file:///etc/rc.conf)         ←  yes this one does get sourced contrary to what is in this file, however on an update, it will probably get cleared back to defailt
[/etc/rc.conf.local](file:///etc/rc.conf.local) ← put local changes to all services here
[/usr/local/etc/rc.conf.d/service-name](file:///usr/local/etc/rc.conf.d/service-name) ← This can be a file or a dir. If a dir, all files are sourced in it. this file is sorced by the service only

Web root
========

/usr/local/www/

