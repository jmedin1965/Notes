# fail2ban and pfBlockerNG
Created Monday 24 February 2025

Greg needed fail2ban to be able to block failed password attempts on his virtual DMZ web servers. Problem is that they are being made available from pfSense HAProxy. So the ban has to happen on the pfsense server

