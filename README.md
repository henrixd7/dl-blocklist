# dl-blocklist

Automated hosts blocklist download in a slightly more secure way.

This script is meant to download IP/host bloclists of malicious hosts, AD servers, trackers or malware networks like:
- Steven Black's list of malicious hosts: https://github.com/StevenBlack/hosts
- Firehol IP-lists: https://iplists.firehol.org/?ipset=firehol_level4
- Yoyo AD servers: https://pgl.yoyo.org/adservers/

Downloaded lists are parsed and filtered to make sure that resulting list is always syntactically valid and filters out some obviously bad things like trying to block localhost or lan addresses. This script supports hosts file syntax and IP/network lists defined below in more detail.

With this script, using programmatically downloaded blocklists with firewalls and other software should be little bit safer.

## Usage

~~~
usage: dl-blocklist [-h] [-l] [-v] url destination

positional arguments:
  url          URL to download blocklist from.
  destination  Destination filename or directory. In case of directory, basename from URL is used as
               filename.

options:
  -h, --help   show this help message and exit
  -l, --lax    set network filter in lax-mode
  -v, --verbose  increase verbosity, max 2 times
~~~


## Examples
###### /root/bin/dl-blocklists
~~~sh
#!/bin/sh
dl-blocklist "https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts" /var/blocklists/steven-black.txt
dl-blocklist "https://pgl.yoyo.org/adservers/serverlist.php?showintro=0;hostformat=hosts" /var/blocklists/yoyo-adservers.txt
dl-blocklist "https://github.com/firehol/blocklist-ipsets/raw/refs/heads/master/firehol_level4.netset" /var/blocklists/firehol_level4.txt
~~~

###### /etc/systemd/system/opensnitch.service.d/download-blocklist.conf
~~~
[Service]
ExecStartPre=/root/bin/dl-blocklists
~~~

###### /etc/cron.daily/dl-blocklists.sh
~~~
#!/bin/sh
cat /etc/hosts.template > tmp_hosts
/root/bin/dl-blocklist "https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts" /tmp/steven-black.txt
cat /tmp/steven-black.txt >> tmp_hosts
rm /tmp/steven-black.txt
cp /etc/hosts /etc/hosts.old
mv tmp_hosts /etc/hosts
~~~

## Supported list types

### hosts file

One IP and domainname pair in each line separated with whitespace, where IP is either "127.0.0.1", "0.0.0.0" or "::1". Any host alias definitions are ignored.

~~~
# this is a comment, it's ignored
0.0.0.0 www.domain.com # this comment is ignored too
127.0.0.1 www.domain.com
~~~

More information on ipaddress.ip\_address(): https://docs.python.org/3/howto/ipaddress.html

### list of IPs / networks

One IP or network definition per line. Lines with more then one definition are ignored.

~~~
# this is a comment, it's ignored
1.2.3.4
5.6.7.8 # this comment is ignored too
1.0.1.0/24
254.0.0.0/8
~~~

###### strict mode (default):
Any host bits can't be set, so network definitions like 254.254.254.254/16 and 255.255.255.255/8 are filtered out.
###### lax mode (with -l or --lax):
Network definitions like 254.254.254.254/16 and 255.255.255.255/8 are allowed.

More information on ipaddress.ip\_network(): https://docs.python.org/3/howto/ipaddress.html
