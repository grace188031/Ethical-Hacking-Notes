
#### For nmap, its called stealth scanning `nmap -sS` (still detectable)
- `nmap -T4 -p- -A` - default is T4 - T1 is slow and T5 is fast(slow to fast)
- `-p-` means top 1000 ports
- `-p` 80,443 - can scan specific ports
- `-A` - scan everything (version, OS, FIngerprinting,script scanning and traceroute)
- `-sU` - scanning UDP
	- When scanning UDP, use the `nmap -sU -T4 -p 192.168.68.6` - we removed -A cause it may take forever to scan everything cause it has no 3 way handshake
- `-sV` - Probe open ports to determine service/version info
- `-sC` - equivalent to script default
- `-Pn` faster assuming you are certain that the all hosts are up and you will not perform host discovery
- `-oN` - to save in standard format .txt

## Install nmap Scripts

- https://www.skullsecurity.org/2010/how-to-install-an-nmap-script-2
	- `find / -name "*.nse"`

Use case

```
──(root㉿kali)-[~]
└─# ls -al /usr/share/nmap/scripts | grep -e "smb"
-rw-r--r-- 1 root root 45061 Jun  1  2023 smb-brute.nse
-rw-r--r-- 1 root root  5289 Jun  1  2023 smb-double-pulsar-backdoor.nse
-rw-r--r-- 1 root root  4840 Jun  1  2023 smb-enum-domains.nse
-rw-r--r-- 1 root root  5971 Jun  1  2023 smb-enum-groups.nse
-rw-r--r-- 1 root root  8043 Jun  1  2023 smb-enum-processes.nse
-rw-r--r-- 1 root root 27274 Jun  1  2023 smb-enum-services.nse
-rw-r--r-- 1 root root 12017 Jun  1  2023 smb-enum-sessions.nse
-rw-r--r-- 1 root root  6923 Jun  1  2023 smb-enum-shares.nse
-rw-r--r-- 1 root root 12527 Jun  1  2023 smb-enum-users.nse
-rw-r--r-- 1 root root  4418 Jun  1  2023 smb-flood.nse
-rw-r--r-- 1 root root  7471 Jun  1  2023 smb-ls.nse
-rw-r--r-- 1 root root  8758 Jun  1  2023 smb-mbenum.nse
-rw-r--r-- 1 root root  8220 Jun  1  2023 smb-os-discovery.nse
-rw-r--r-- 1 root root  4982 Jun  1  2023 smb-print-text.nse
-rw-r--r-- 1 root root  1833 Jun  1  2023 smb-protocols.nse

```


