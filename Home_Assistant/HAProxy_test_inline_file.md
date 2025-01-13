# HAProxy test inline file
Created Monday 11 November 2024

Just to test which HAProxy you are actuall hitting, you can set up a backend to just dish out a small text file.

REF: <https://webmasters.stackexchange.com/questions/109942/haproxy-as-web-server>

The accepted answer of "no, it is not designed to do that" is correct.

But software being what it is, if you DO still want to do it, there is a hacky workaround.

You need an ACL pointing the request to a custom backend. For this example lets say you wanted to serve robots.txt

frontend port80
acl is_robotstxt path /robots.txt
use_backend robots if is_robotstxt
backend robots
mode http
errorfile 503 /etc/haproxy/errors/robots.http
Note that there are no servers defined for this backend, so when /robots.txt is requested and haproxy uses it, it serves a 503 error. We specify that 503 errors should serve /etc/haproxy/errors/robots.http, which luckily specifies the full HTTP output headers and all.

So in that file, we put

HTTP/1.0 200 Found
Cache-Control: no-cache
Connection: close
Content-Type: text/plain

Content here
	
so now what should be a 503 error is actually a 200 Found reply, content and all.

Note that doing this is not really recommended and comes with many limitations. Most obvious is that you can only serve one file this way per backend. Less obvious is that this file must fit inside haproxy's BUFSIZE, which is usually 8 or 16kB. Haproxy also is not doing any kind of sanitization on the file served this way, so its on you to serve the right headers the right way. If your clients need \r\n instead of just \n, thats on you to handle.

You're much better off just setting up a copy of your webserver of choice and just having a "static asset backend" haproxy can route to.

