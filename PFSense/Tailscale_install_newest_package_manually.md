# Tailscale install newest package manually
Created Friday 14 February 2025

REF: <https://forum.netgate.com/topic/174525/how-to-update-to-the-latest-tailscale-version/85?lang=en-x-pirate>

pkg add -f <https://pkg.freebsd.org/FreeBSD:14:amd64/latest/All/tailscale-1.78.1.pkg>

pkg add -f <https://pkg.freebsd.org/FreeBSD:14:amd64/latest/All/tailscale-1.80.2.pkg>

So long as I can get the direct package location, I should be able to install amy package and possibly create my own which is even more interesting.

have a script to install a package direct from freeBSD now:

[/usr/local/scripts/sbin/pfs-install_from_freebsd](file:///usr/local/scripts/sbin/pfs-install_from_freebsd)

have to issue this to connect manually

/usr/local/bin/tailscale up --accept-routes --advertise-exit-node --advertise-routes=192.168.10.0/23,192.168.20.0/23,192.168.30.128/27,192.168.30.0/29

