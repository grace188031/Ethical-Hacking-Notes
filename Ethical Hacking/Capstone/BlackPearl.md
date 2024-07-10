
#blackpearl

IP: `192.168.64.8`



## Scanning and enumeration

- nmap -Pn -p- -A -T4 -oN nmapblackpearl.txt 192.168.64.8
```
┌──(root㉿kali)-[~]
└─# nmap -Pn -p- -A -T4 -oN nmapblackpearl.txt 192.168.64.8
Starting Nmap 7.94 ( https://nmap.org ) at 2024-07-10 09:45 PDT
Nmap scan report for 192.168.64.8
Host is up (0.0018s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 66:38:14:50:ae:7d:ab:39:72:bf:41:9c:39:25:1a:0f (RSA)
|   256 a6:2e:77:71:c6:49:6f:d5:73:e9:22:7d:8b:1c:a9:c6 (ECDSA)
|_  256 89:0b:73:c1:53:c8:e1:88:5e:c3:16:de:d1:e5:26:0d (ED25519)
53/tcp open  domain  ISC BIND 9.11.5-P4-5.1+deb10u5 (Debian Linux)
| dns-nsid: 
|_  bind.version: 9.11.5-P4-5.1+deb10u5-Debian
80/tcp open  http    nginx 1.14.2
|_http-title: Welcome to nginx!
|_http-server-header: nginx/1.14.2
MAC Address: BE:06:26:D7:D2:9F (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.8
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   1.84 ms 192.168.64.8

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.36 seconds
```

### Enumerating port 80

- No result in ffuf, no directories detected using the `ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://192.168.64.8/FUZZ -mc 200,204,301,302,307,401,403,405`
- Checking the source code of the default website, we see the webmaster: `alek@blackpearl.tcm`

![[Pasted image 20240710125702.png]]


### Enumerating port 53

#dnsreconaissance

- DNS reconaissance to the victim using `dnsrecon -r 127.0.0.0/24 -n 192.168.64.8 -d lk`, we saw that the domain of the victim is **blackpearl.com**
```
┌──(root㉿kali)-[~]
└─# dnsrecon -r 127.0.0.0/24 -n 192.168.64.8 -d lk
[*] Performing Reverse Lookup from 127.0.0.0 to 127.0.0.255
[+]      PTR blackpearl.tcm 127.0.0.1
[+] 1 Records Found

```

- COnfigure the 