# dl-blocklist

Automated blocklist download in a slightly more secure way.

This script parses the downloaded blocklist and filters items to make sure that result is always syntactically intact and filters some obvious things like blocking localhost.

With this script, using automatically downloaded blocklists with blocking rules should be little bit safer.

## Usage

~~~
usage: dl-blocklist [-h] [-l] url destination

positional arguments:
  url          URL to download blocklist from.
  destination  Destination filename or directory. In case of directory, basename from URL is used as
               filename.

options:
  -h, --help   show this help message and exit
  -l, --lax    set network filter in lax-mode
~~~


## Examples
~~~sh
#!/bin/sh
dl-blocklist "https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts" /var/blocklists/steven-black.txt
dl-blocklist "https://pgl.yoyo.org/adservers/serverlist.php?showintro=0;hostformat=hosts" /var/blocklists/yoyo-adservers.txt
dl-blocklist "https://github.com/firehol/blocklist-ipsets/raw/refs/heads/master/firehol_level4.netset" /var/blocklists/firehol_level4.txt
~~~

## Supported list types

#### hosts file like syntax

One IP and canonical hostname pair in each line separated with space, where IP is either "127.0.0.1", "0.0.0.0" or "::1". Alias definitions are ignored. Valid IP's are filtered out as not being valid canonical hostnames but invalid addresses, like 999.999.999.999, are not filtered.

~~~
# this is a comment, it's ignored
0.0.0.0 www.domain.com # this comment is ignored too
127.0.0.1 www.domain.com
~~~

More information on ipaddress.ip\_address(): https://docs.python.org/3/howto/ipaddress.html

#### list of IPs / networks

One IP or network definition per line. Lines with more then one definition are ignored.

#### strict mode (default)
Network definitions like 254.254.254.254/8 and 255.255.255.255/8 are filtered.
#### lax mode (with -l or --lax)
Network definitions like 254.254.254.254/8 and 255.255.255.255/8 are allowed.

More information on ipaddress.ip\_network(): https://docs.python.org/3/howto/ipaddress.html

~~~
# this is a comment, it's ignored
1.2.3.4
5.6.7.8 # this comment is ignored too
1.0.1.0/24
254.0.0.0/8
~~~
