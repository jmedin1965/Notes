# Home Assistant
Created Tuesday 20 August 2024

Change Reolink IP camera IP address
-----------------------------------

Yet again, I have changed the IP address of the Realink IP camera. The IP address needs to be changed in two places on the following file:

[/root/config/.storage/core.device_registry](file:///root/config/.storage/core.device_registry)
[/root/config/.storage/](file:///root/config/.storage/core.device_registry)core.device_registry

config is a symbolic link to [/homeassistant](file:///homeassistant)

you will have to look for the old IP address or the mac address.

Once changed you need to reload HA from settings>>system, then on this page there is a power icon top right of screen. Click it and choose "Restart Home Assistant"

Change IP of sonoffs
--------------------

Looks like this requires no work if you have firewall opened to MQTT server




