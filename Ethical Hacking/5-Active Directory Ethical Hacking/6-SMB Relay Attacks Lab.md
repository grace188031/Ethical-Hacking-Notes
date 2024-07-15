#smbrelayattack
#smbsigningdisabled
## SMB Relay Attacks Lab

1. Identify the host without SMB signing enabled
Command: `nmap script=smb2-security-mode.nse -p445 192.168.64.220 -Pn`

`-Pn` - Treats all hosts as online, skipping host discovery. This is useful if you know the host is up but it isn't responding to ICMP echo requests (ping).


Here is the result

- For the domain controller

```
┌──(root㉿kali)-[~]
└─# nmap --script=smb2-security-mode.nse -p445 192.168.64.250 -Pn
Starting Nmap 7.94 ( https://nmap.org ) at 2024-07-14 16:59 PDT
Nmap scan report for 192.168.64.250
Host is up (0.0060s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 16:91:DE:D1:61:68 (Unknown)

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

Nmap done: 1 IP address (1 host up) scanned in 0.21 seconds

```