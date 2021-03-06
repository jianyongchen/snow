snow
====

snow is a layer 3 virtual network that abstracts the underlying network and allows public keys to be used in place of IP addresses. It can be used even with existing IPv4 applications over IPv4 or IPv6 networks and transparently provides end-to-end encryption and NAT traversal.

<a href=http://trustiosity.com/snow>trustiosity.com/snow</a>

Build
-----

Dependencies:

*	C++11 compiler (e.g. g++-4.7 or newer), make
*	OpenSSL 1.0.1 (libssl)
*	optional: libminiupnpc (1.5) and libnatpmp

<p></p>
	# apt-get install g++ make libssl-dev  
	(optional):  
	# apt-get install libminiupnpc-dev libnatpmp-dev

	$ cd src  
	$ make -j5

If you don't want libminiupnpc/libnatpmp (or you got some error because e.g. you have a different version of libminupnpc) then you can do this:

	$  make -j5 NO_UPNP=1 NO_NATPMP=1

Install
-------

	# make install

For additional installation and configuration information see <a href=http://trustiosity.com/snow/how-to-install.html>trustiosity.com/snow/how-to-install.html</a>

Use
---

When you run snow your machine gets a key name like this: baaaac6rxjmmenb7m5txgpe3nmmrrh4z4ohcr7sxqkrvbk4csbrrorpw7.key.

	$ grep "Your key is" /var/log/syslog  
	snow: Your key is baaaac6rxjmmenb7m5txgpe3nmmrrh4z4ohcr7sxqkrvbk4csbrrorpw7.key

When snow is running applications can resolve key names to an IP address. Packets sent to and from that IP address are transparently delivered to and from the machine with that key over a datagram TLS tunnel, e.g.:

	$ ping baaaac6rxjmmenb7m5txgpe3nmmrrh4z4ohcr7sxqkrvbk4csbrrorpw7.key  
	PING baaaac6rxjmmenb7m5txgpe3nmmrrh4z4ohcr7sxqkrvbk4csbrrorpw7.key (172.16.0.2) 56(84) bytes of data.  
	64 bytes from baaaac6rxjmmenb7m5txgpe3nmmrrh4z4ohcr7sxqkrvbk4csbrrorpw7.key (172.16.0.2): icmp_req=1 ttl=64 time=0.611 ms  
	64 bytes from baaaac6rxjmmenb7m5txgpe3nmmrrh4z4ohcr7sxqkrvbk4csbrrorpw7.key (172.16.0.2): icmp_req=2 ttl=64 time=0.588 ms  
	^C

Although snow keys are technically names, they are better used in contexts where you might currently use IP addresses.

sdns
====

sdns is a small DNS resolver daemon. It was created for use with snow but can also be used as a standalone portable recursive caching DNS resolver. 

sdns also allows DNS CNAME aliases to be used for key names:

	# echo CNAME alice baaaac6rxjmmenb7m5txgpe3nmmrrh4z4ohcr7sxqkrvbk4csbrrorpw7.key >> /etc/sdns/local.names

	$ ping alice  
	PING baaaac6rxjmmenb7m5txgpe3nmmrrh4z4ohcr7sxqkrvbk4csbrrorpw7.key (172.16.0.2) 56(84) bytes of data.  
	64 bytes from baaaac6rxjmmenb7m5txgpe3nmmrrh4z4ohcr7sxqkrvbk4csbrrorpw7.key (172.16.0.2): icmp_req=1 ttl=64 time=0.614 ms  
	64 bytes from baaaac6rxjmmenb7m5txgpe3nmmrrh4z4ohcr7sxqkrvbk4csbrrorpw7.key (172.16.0.2): icmp_req=2 ttl=64 time=0.603 ms  
	^C  

sdns operates recursively by default, if you want you can set an upstream DNS server like this:

	# echo 8.8.4.4 8.8.8.8 > /etc/sdns/forwarders/@


