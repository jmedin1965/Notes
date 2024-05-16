# Log rotation vi newsyslog.conf
Created Thursday 16 May 2024

Log rotation in freebsd is done by creating a vile in [/etc/newsyslog.conf.d/.](file:///etc/newsyslog.conf.d) See man newsyslog.conf for details


* ☐ log rotate qemu agent log file. It is getting very large. PID file is [/var/run/qemu-ga.pid.](file:///var/run/qemu-ga.pid.) It is being created from /etc/rc.conf.local


