# mqtt
Created Monday 02 September 2024



I think I will go with default topic and full topic config which is based on mac address. I will just customise the device name and Friendly name but I got these  the wrong way around on the following. No they have to be reversed because the device name is the one that shows up in the main menu. This is changed in configuration>>Configure Other

pfs03
-----

host01
------

topic:       sonoff
Full Topic:  %prefix%/%topic%/

192.168.10.40

10.11.3.9	 c4:4f:33:87:92:e2	sonoff-host01	host01 sonoff	n/a	n/a	 
 	10.11.3.8	 c4:4f:33:87:85:fa	esxi-disk-shelf01	esxi-disk-shelf01	n/a	n/a	  
 	10.11.3.7	 3c:71:bf:38:9f:10	sonoff-garage-door01	sonoff-garage-door01	n/a	n/a	  
 	10.11.3.6	 c4:4f:33:87:d4:9d	sonoff-esxi-02	sonoff-esxi-02	n/a	n/a	 
 	10.11.3.5	 dc:4f:22:e5:bb:f0	sonnof-laptop-media-room01	sonnof-laptop-media-room01	n/a	n/a	 
 	10.11.3.4	 c4:4f:33:87:a1:87	tasmota-87A187-0391	tasmota-87A187-0391	n/a	n/a	 
 	10.11.3.3	 60:01:94:bb:37:e7	sonoff-6119	sonoff-6119 disk shelf	n/a	n/a	 
 	10.11.3.2	 60:01:94:bb:8d:9a	sonoff-3482jm-sonoff-esxi-02	sonoff-3482 jm-sonoff-esxi-02	n/a	n/a	  
 	10.11.3.10 5c:cf:7f:7c:bc:46	sonoff-7238jm-exsi-02	sonoff-7238 jm-exsi-02	n/a	n/a	  

 	10.11.3.2	60:01:94:bb:8d:9a	sonoff-3482jm-sonoff-esxi-02	sonoff-3482 jm-sonoff-esxi-02	n/a	n/a	  
 	10.11.3.3	60:01:94:bb:37:e7	sonoff-6119	sonoff-6119 disk shelf	n/a	n/a	 
 	10.11.3.4	c4:4f:33:87:a1:87	tasmota-87A187-0391	tasmota-87A187-0391	n/a	n/a	 
 	10.11.3.5	dc:4f:22:e5:bb:f0	sonnof-laptop-media-room01	sonnof-laptop-media-room01	n/a	n/a	 
10.11.3.9	c4:4f:33:87:92:e2	sonoff-host01	host01 sonoff	n/a	n/a	 


 	10.11.3.2		60:01:94:bb:8d:9a	sonoff-3482jm-sonoff-esxi-02	sonoff-3482 jm-sonoff-esxi-02	n/a	n/a	  
 	10.11.3.3		60:01:94:bb:37:e7	sonoff-6119	sonoff-6119 disk shelf	n/a	n/a	 
pfs03 	192.168.21.16	c4:4f:33:87:a1:87	tasmota-87A187-0391	tasmota-87A187-0391	n/a	n/a	 
 	10.11.3.5		dc:4f:22:e5:bb:f0	sonnof-laptop-media-room01	sonnof-laptop-media-room01	n/a	n/a	 
host01	192.168.21.36	c4:4f:33:87:92:e2	sonoff-host01	host01 sonoff	n/a	n/a	


192.168.30.129 - 192.168.30.158
192.168.30.158/27
192.168.30.129 pfs01
192.168.30.130 pfs02
192.168.30.131 pfs03
192.168.30.132 pfs04



192.168.21.186	dc:4f:22:e5:bb:f0


host01
topic:       sonoff
Full Topic:  %prefix%/%topic%/

