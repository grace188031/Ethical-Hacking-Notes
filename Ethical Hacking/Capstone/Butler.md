
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

Source:
- https://blog.pentesteracademy.com/abusing-jenkins-groovy-script-console-to-get-shell-98b951fa64a6
- https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76 - githubgroovyreverseshell