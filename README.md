# dl-blocklist

Automated blocklist download in a slightly more secure way.

This script parses the downloaded blocklist and filters items to make sure that result is always syntactically intact and nothing malicious or harmful couldn't be sneaked in.

With this script, using automatically downloaded blocklists should be little bit safer with blocking rules.

## Usage

dl-blocklist \<src url> \<dst path> 
- src url: URL for blocklist download
- dst path: destination path to save filtered blocklist to, if destination is directory, basename from url is used as filename

## Examples
  #!/bin/sh<br>
  dl-blocklist "https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts" /var/blocklists/steven-black.txt<br>
  dl-blocklist "https://pgl.yoyo.org/adservers/serverlist.php?showintro=0;hostformat=hosts" /var/blocklists/yoyo-adservers.txt<br>
  dl-blocklist "https://github.com/firehol/blocklist-ipsets/raw/refs/heads/master/firehol_level4.netset" /var/blocklists/firehol_level4.txt<br>

## Supported list types

#### hosts file

  \# this is a comment, it's ignored<br>
  0.0.0.0 www.domain.com # this is ignored<br>
  127.0.0.1 www.domain.com<br>

#### list of IPs / networks

  \# this is a comment, it's ignored<br>
  1.2.3.4<br>
  5.6.7.8 # this is ignored<br>
  1.0.1.0/24<br>
  254.0.0.0/8<br>
