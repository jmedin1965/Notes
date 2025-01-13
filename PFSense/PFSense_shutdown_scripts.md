# PFSense shutdown scripts
Created Thursday 16 May 2024

Looks like you can create shutdown scripts. They are run from /etc/pfSense-rc.shutdown[.]() At the end, this script runs all executable scripts at [/usr/local/etc/rc.d/shutdown.*.sh.](file:///usr/local/etc/rc.d/shutdown.%2A.sh.) I have created a script on pfs03, which is a physical server. This script signals the controlling sonoff to turn off after 30 seconds.

Looks like this script is also run on startup, so be carefull. It is run with first argument as start.

Also, any script in [/usr/local/etc/rc.d/](file:///usr/local/etc/rc.d) that ends in .sh than is executable is also run. Check first arg as this script is run as an rc script, so star|stop|restart

REF: <https://docs.netgate.com/pfsense/en/latest/development/boot-commands.html>

